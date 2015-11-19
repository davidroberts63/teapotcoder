title: Generate screenshots of hexo websites during build
date: 2015-11-07 00:00:00
tags: screenshots phantomjs hexo javascript circleci
---
In this post I continue detailing the [automation of hexo.io][] by generating screenshots of the generated site. This is primarily useful when it is run in the continuous integration process so I can quickly check on content submissions.
<!--more-->
Something you have to understand is asynchronous work in JavaScript. Callbacks, async, promises, promises, promises. I don't need a promise, just need it to work. Eventually I chose the [async][] npm package because I got it to work and the `queue` made sense for the moment. The `queue` function will call the given task function for every item in the queue. Here is an example of using async.queue.

```
var async = require("async")

// Define the 'task' objects which
// will be passed into the queue function
var tasks = [
     {name: "John", age:42},
     {name: "Susan", age:35}
];

// Create new queue passing in the function that will be
// called for every task object pushed to the queue
var queue = async.queue(function(task, callback) {
  console.log("%s is %d years old.", task.name, task.age);
  
  // If there was an error pass it into the callback
  callback();
});

// Define a function to be called when the last item
// in the queue is returned from the worker/task function
queue.drain = function(err) {
     console.log("Last item returned from worker");
     console.log("Err (if any):");
     console.log(err);
};

// Filling the queue
queue.push(tasks[0]);
queue.push(tasks[1]);
console.log("Done");
```
Each item will be processed concurrently if possible. There is no guarantee as to the order the task objects will be processed. So make sure to do some setup before hand if needed. You can view the [whole file][] on GitHub if you want. But I will be going over each part below.

My goal for this step of my hexo site deployment is to generate screenshots. For that I use [PhantomJS]. With PhantomJS I can simulate the browser at various window sizes and urls snapping screenshots along the way. First up, get the tools for the job. After installing PhantomJS and the `async` npm package I start a `screenshots.js` file.

Get the needed modules for the script.
```
var system = require("system");
var webpage = require("webpage");
var async = require("async");
```

The first two modules are provided by PhantomJS. `system` is how you interact with the machine. It's what you use instead of `process` in Node. `webpage` is well, the webpage you are browsing with PhantomJS. `async` is what I'm using to handle the asynchronous work here.

Setup the destination for the screenshots and the sizes of screenshots desired.

```javascript
var destination = system.env["CIRCLE_ARTIFACTS"] || "screenshots";
var sizes = [
	{ width: 1024, height: 768 }, // Desktop
	{ width: 750,  height: 1334}  // iPhone 6
];
```

Here is where we use `system` to get an environment variable or just use `screenshots` as a default when run locally. `CIRCLE_ARTIFACTS` is set by CircleCI during the build process. Once the build is done CircleCI will retain any files stored in that location along with the build. I did a short amount of Googling for the iPhone screenshot size so if it's inaccurate just let me know.

Next startup a new queue.

```javascript
var queue = async.queue(handle);
queue.drain = done;
queue.pause();
primeTheQueue();
```

Specify the function that will `handle` the tasks. I also like to know when the process is complete. That's what the `drain` member is for. The `done` method will be called once all is...well, done. The `queue.pause` may actually not be required, I have not really tested it but I know it does what it says. It pauses the queue from processing until told otherwise. That last call to `primeTheQueue` does just that. It loads up the queue with the tasks to process.

Let's look into how the tasks get into the queue first. Each task object looks like this:

```javascript
{
  name: "Name of page for display purposes",
  url: "Full url of page to open",
  page: {PhantomJS webpage object},
  size: {width: x, height: y}
}
```

The `name` and `url` should be obvious. The `page` and `size` are used to indicate a different step in the screenshot generation. I'll get to them in just a bit so ignore them for the moment. Here is the `primeTheQueue` function:

```
function primeTheQueue() {
  queue.push({name: "home", url: "http://localhost:4000/"}, errorOut);

  var home = webpage.create();
  home.open("http://localhost:4000/", function() {
	var firstPost = this.evaluate(function() {
      console.log("Getting first article");
	  return document.querySelector("section#main article:first-child a.article-title");
	});
	
	queue.push({name: "post", url: firstPost.href}, errorOut);
	queue.resume();
  });
}
```
I always want screenshots of the home page so just add that immediately. But I also want a screenshot of the most recent post. To get that I need to open the webpage and find that first link. Create the webpage and open to the home page. The callback will then `evaluate` the css query selection on that webpage. That query selector path is specific to the layout of our website template. So it will be different for you most likely. It just finds the link to the first post on the page. Add a new task object to the queue with an appropriate name and the url from the webpage. I also add an `errorOut` function to report problems processing that particular task object.

Next let's take a look at the function that is called for each task object.

```
function handle(task, callback) {
  if(task.page === undefined) {
    open(task, callback);
  } else {
    renderPage(task, callback);
  }
}
```

Short and simple, it just passes onto an appropriate processing method based on if the url has been opened in PhantomJS already. If it has already been opened, as indicated by a defined `task.page` member on the task object, then render the page out to file. If it has not been opened then open it.

Speaking of opening a url.

```
function open(task, callback) {
  console.log("Opening " + task.name);
  var page = webpage.create();
  
  page.open(task.url, function() {
    for(var i = 0; i < sizes.length; i++) {
      queue.push({name: task.name, size: sizes[i], page: this});
    }
    callback();
  });
}
```

The `open` function will be called when a task object is to be processed but it does not have a defined `page` member. `open` creates the new PhantomJS `webpage` object; opens the url specified in the task object; then pushes additional task objects onto the same queue but providing the `webpage` object used to open that url. Note that it's not just one task object for each url to open; it's a task object per url per window size to render. That `callback` must be called for the queue to know that we have finished with this task object. If there was a problem we could pass the `callback` function an error object.

Eventually the queue will have to process a task object that has been opened. Which the `handle` function will pass off to `renderPage`.

```javascript
function renderPage(task, callback) {
  console.log("Rendering " + task.name + " at " + task.size.width + "x" + task.size.height);
  var page = task.page;
  page.viewportSize = task.size;
  page.clipRect = { top: 0, left: 0, width: task.size.width, height: (task.size.height * 2) };
  
  var renderPath = destination + "/" + task.name + "-" + page.clipRect.width + "x" + page.clipRect.height + ".png"
  page.render(renderPath);
  
  callback();
}
```

Here we set the size of the `page`. That simulates the size of the browser window. The `clipRect` is set taller so that I grab more content. Think of it like taking a screenshot of a web page while scrolling down the page a bit more. Then just tell the `page` to render to the path defined. Using the name plus size gives me decently unique name for the file. Each build has it's own artifacts so I'm not going to bump into a file from a previous build with the same name.

The last functions are the ones for when the queue has been drained of all task objects, as well as an error function to report any problems when processing a task.

```
function done() {
  console.log("Done");
  phantom.exit();
}

function errorOut(ex) {
  if(ex !== undefined) {
    console.log("ERROR on task " + ex);
    phantom.exit();
  }
}
```

Please note that it's important to put the `phantom.exit();` inside the `done` function. If you put it outside of that (for example at the end of the js file) then your process will just sit there and do nothing. Why is that? Because if `phantom.exit();` was outside the functions it would get called before the queue is empty. Which would result in the `webpage` objects not responding. It's important to pay attention to what actually happens at what time with asynchronous calls. It can get weird.

Now just run the script.
```shell
phantomjs screenshots.js
```

Then check in the artifacts directory (screenshots locally). You should see some image files. Adding this to your CI process will depend on which tool you use. For CircleCI it's an edit to your `circle.yml` file.

```yaml
test:
  override:
    - npm install -g hexo-cli
    - hexo server:
        background: true
    - sleep 2
    - phantomjs ./screenshots.js

```

In CirceCI the `test` field in your yml file is what they use to run custom tests. What I'm doing here is making sure hexo is available for me to use. I start the `hexo server` just like you would do locally. This is what PhantomJS will call to get the website.

Note that I specify that `background: true` field for the `hexo server` call. That's special for CircleCI. Each command  is a separate SSH connection. So if you just `hexo server` the hexo server would stop running as soon as the SSH connection closed and moved onto the next command. But with that additional field set CircleCI knows to run that command in the background to keep it going even after the SSH connection closes.

I sleep for a few seconds to give hexo time to generate. Two seconds is plenty of time. Then just run the screenshot script in PhantomJS. Once it's done the 'tests' are complete. CircleCI will gather the artifacts which happen to be screenshots.
 
There are a few improvements I want to make to this. Namely the ability to do more than just the home and the post, although I may call YAGNI on it. But I would like to use that excuse in order to get a better handle on the asynchronous patterns. I'd also like to make this a NPM package sometime. To make it easier for others to utilize this functionality.

In my next post I'll talk about using node.js to send those screenshots to a GitHub PR as a comment. Take a look at some of the closed pull requets on the [okcsharp website repository][] to see what it looks like.

[automation of hexo.io]:/post/automating-static-sites-with-hexo-io-circleci-and-github-pages/
[PhantomJS]:http://phantomjs.org/
[async]:https://www.npmjs.com/search?q=async
[An absolute beginner's guide to node.js]:http://blog.modulus.io/absolute-beginners-guide-to-nodejs
[whole file]:https://github.com/techlahoma/okcsharp-website/blob/df853f0a9a337c4a8687cce82790960cdaf50530/screenshots.js
[okcsharp website repository]:https://github.com/techlahoma/okcsharp-website/pulls?q=is%3Apr+is%3Aclosed