title: Post site screenshots to Github PR
tags: 'node, github, screenshots, hexo, heroku, express'
date: 2015-11-18 23:41:21
---

I continue the [automation of hexo.io][] by taking the [website screenshots][] generated during the continuous integration proccess and posting them to the Github PR as a comment.
<!--more-->
The code you are about to see is not the prettiest JavaScript you will see. But I felt it would be beneficial to see this code evolve over time. I'll make additional posts talking about how I improve things with this. You can see the full repository on my Github account, [suede-halibut][].

### What it does
This node app is just a RESTish/ful/whatever API. When CircleCI is done with the build it will issue a POST to the '/pr-build-completion' url of my deployed heroku app. The request body from CircleCI contains the project name, the build number and the pull request number among many other things. The app will call into CircleCI to get the artifacts for that build number of that project. Then take the image artifacts and post them to the Github PR that triggered the CircleCI build.

### How it does it
First thing to do. Pick a Node.js web application framework. For this I picked [Express.js][] because it seemed the simplest to work with for such a simple app as this.

#### Getting all the things
My `app.js` is where my Express.js app lives and is configured. The first part contains all the `require`ments and initial objects.

```
var request = require("request");
var poster = require("./postissue");

var bodyParser = require("body-parser");
var express = require("express");
var app = express();
var router = express.Router();
```

`request` is for making HTTP requests to CircleCI. `poster` is my own module that does the actual posting of images to the Github PR. `bodyParser` is Express.js middleware for parsing the request body as JSON. Then you see Express.js, the creation of the app and getting the Express.js router.

#### Get the configuration
I need some private information to be able to talk to CircleCI and to Github. These environment variables are set in heroku manually.

```javascript
console.log("Getting settings");
var settings = {};
settings.circleCiAccount = process.env["CIRCLE_CI_ACCOUNT"];
settings.circleCiToken = process.env["CIRCLE_CI_TOKEN"];
settings.githubUsername = process.env["GITHUB_USERNAME"];
settings.githubApiKey = process.env["GITHUB_APIKEY"];
settings.orgName = process.env["GITHUB_ORGNAME"];
```

The way it's setup right now is that it only works for one Github organization/account. At the time of writing that's just [techlahoma][]. That's the same for the CircleCI part as well because it's using the name of the Github account as the CI account. It was a little confusing at first but CircleCI just 'follows' a repository so goes along with that nomenclature. Just using API keys for CircleCI and Github. You never want to have to use your actual username and password for automation. An API key shows up as you but can't be used to login as you to the website. So it's likely not able to change your normal login password.

#### Smoke test route and request logging.
Because I'm new to Express.js and to Node apps in general I wanted to create a simple route in Express.js that would let me know I did at least one thing right. Also, because Express.js is awesome it was trivial to add my own middleware to log requests coming in.

```javascript
router.use(function(req, res, next) {
	console.log("Incoming: %s %s", req.method, req.url);
	next();
});

router.get("/", function(req, res) {
	res.send("Ready when you are");
});
```

If you are not familiar with Express.js most of that should still make some sense. First the log part. `router.use` inserts middleware into the request pipeline. In this case it is an anonymous function that just sends some of the details of the request to the console. `next();` is the callback I have to call to allow the pipeline to continue. If I don't call that function then the pipeline stops there. This happens for every request that comes in that is matched to any route.

The `router.get` defines a route that Express.js will respond to. The `get` part defines the HTTP method. Then you see the URL path which is the root of the site in this case. The function called by Express.js receives the request object and a response object to write a response. All I do here is write out a message that let's me know I actually hit the site.

#### The POST
The next route is the main one for this app. It's the only other route defined. It's a bit long so I'll step through it in chunks.

```javascript
router.post("/pr-build-completion", bodyParser.json(), function(req, res) {
```

The route is defined to respond to POST requests made to the `/pr-build-completion` url. That `bodyParser.json()` is middleware injection. Actually what's really going on is the first parameter is the path to respond to; the rest are just taken by Express.js as a series of callbacks to execute for the matching request. Check out the docs on [router methods][]. I really like this part of Express.js. Make's it very easy to individually break up request handling. But that's for another post.

Then I get the relevant data from the request body.

```javascript
console.log("POST build completion");
var project = req.body.payload.reponame;
var build = req.body.payload.build_num;
var pull = req.body.payload.pull_request_urls[0];
```

I get the name of the repository that triggered the build, the build number in CircleCI the artifacts of interest are located, and the pull request urls in Github that triggered the build.

You'll notice it says `pull_request_urls` and I just get the first one. I am not sure of a situation where multiple PRs will trigger a single build but in any case there should be one here. Actually there won't be during builds triggered by merging to `master` but that's a defect that I'll deal with in a later post.

Next I tease out the PR number.

```javascript
pull = pull.substring(pull.lastIndexOf("/") + 1);
console.log("--for build %s of pull %s in the %s project", build, pull, project);
```

I just need the number so I know which PR to add the comment to a bit later.

Build the CircleCI url to get the build artifacts.

```javascript
var url = "https://circleci.com/api/v1/project/" + settings.circleCiAccount + "/" + project + "/" + build + "/artifacts?circle-token=" + settings.circleCiToken;
```

That's using some of the configuration settings we got from the environment earlier. [CircleCI REST API][]

Now that I have the CircleCI url to get the artifacts just send the request.

```javascript
request({url: url, headers: { "Accept": "application/json"} }, function(err, response, body) {
```

Specifying the url and saying we want JSON back in the response. Remember this is the HTTP request going out to CircleCI to get the artifacts for the build.

The first thing to do with the response is parse the JSON body and get just the url for any images in the artifacts.

```javascript
console.log("Retrieved the artifacts payload");
var payload = JSON.parse(body);
var screenshots = payload.map(theUrl).filter(forImages);
console.log(screenshots);
```

This is not Express.js routing thus why I'm parsing the JSON manually. The payload is an array of urls to the artifacts for the build. This is a separate HTTP request going out. Also note I'm going a bit functional here. I had fun with this part as I had just watched a few [JavaScript videos][] by Mattias P Johansson aka [mpjme][]. Very educational and enjoyable to watch. I highly recommend you watch them soon. Anyway I have two functions defined at the end of `app.js` to handle that `map` and `filter` of the parsed payload from CircleCI.

```javascript
function theUrl(artifact) {
  return { url: artifact.url,
    name: artifact.url.substring(artifact.url.lastIndexOf("/") + 1, artifact.url.lastIndexOf("-"))
  };
}

function forImages(image) {
  return image.url.substring(image.url.length - 3) == "png";
}
```
`theUrl` just maps the larger artifact object to just it's url and a name to use in the PR comment. `forImages` filters out everything except for the screenshots generated during the build. When used in the mapping and filtering of the payload I really like how it reads.

Back to the handling of the artifact response. After getting just the data we need from the payload we apply a little configuration and use my `poster` module to send the images to the Github PR.

```javascript
console.log("Posting to Github PR");
settings.prNumber = pull;
settings.repoName = project;

poster.postImagesToIssue(settings, screenshots, function() {
  res.type("json");
  res.json({completum: "yep"});
});
```
First off, I don't like that I'm using the `settings` object to communicate additional details for the `poster` to use. At least I'm putting that in as a parameter but it still feels a bit off. That'll be a later post. But I give it those settings so it knows where to put the screenshots supplied in the second argument. Finally a callback that will just return a simple 'yep' to the originall caller. CircleCI in this case but I do the 'yep' for testing.

#### Starting Express.js
So we've defined the primary route we want. Now we tell Express.js to start listening.

```javascript
app.use("/", router);

var port = process.env["PORT"] || 8080;
var ipaddress = "127.0.0.1";

console.log("Preparing to listen on %s:%d", ipaddress, port);
var server = app.listen(port, function() {
  console.log('%s: Node server started on %s:%d ...', Date(Date.now() ), ipaddress, port);
});
```

First we tell Express.js to use '/' as the base path for all routes defined in the `router` object. This is useful in the case where you have different sections of your app and you don't want to have to repeate the same root part of the path. Think of an admin section verses the normal part of the website.

Then get the port and ip address that will be used to tell Express.js what to listen on. I originally did this on OpenShift so that's where the IP address comes in but it's not used on heroku.

>See, real code, none of that nice, clean, prepared stuff.

#### The poster
The `poster` is my own module in `postissue.js`. It's what takes the urls of the screenshots and puts them in the Github PR comment. When it' called the first thing we do is prepare the info for connecting to the Github API.

```javascript
var GithubApi = require("github");

module.exports.postImagesToIssue = function(settings, cdnUrls, callback) {
  var github = new GithubApi({
    version: "3.0.0",
    protocol: "https",
    host: "api.github.com",
    timeout: 5000
  });
```

I'm using the [Github package][] to handle all the HTTP requests to Github's api. 

```javascript
github.authenticate({
  type: "basic",
  username: settings.githubUsername,
  password: settings.githubApiKey
});
```
Setup the authentication. You can generate those API tokens at the [Personal access tokens][] page of your profile.

Next just generate the markdown for the comment to post to the PR.

```javascript
var comment = "";
for(var image of cdnUrls) {
  comment += image.name + "\r\n" + "![" + image.name + " screenshot](" + image.url + ")\r\n";
}
```

I'm simply concatenating the name of the image and the url to it for each image that was generated during the build. The '\!\[...](...)' part is the markdown for pulling in an image.

Finally I create the comment on the PR.

```javascript
console.log("Issue comment:");
console.log(comment);

github.issues.createComment({
  user: settings.orgName,
  repo: settings.repoName,
  number: settings.prNumber,
  body: comment
}, function(err, res) {
  console.log(err);
  console.log("==================");
  console.log(res);
});

callback(null);
```

First some logging for diagnosis later on when needed. The user in this case will be 'techlahoma'.  The repo is equivalent to the website. 'okcsharp-website' in my case at the moment. But that is gathered from what CircleCI tells us. So if I just setup a different build within the techlahoma organization it should just work. The pull request number triggering the build and then finally the comment with the markdown.

Once that request has completed it executes the callback function provided. It just sends the results to the console for diagnosis later on when needed.

The last thing I do here is execute the callback given. Which in this case just allowes the route handling to continue by writing the response.

#### In closing
Overall it works very well. You can push a commit to a PR and stay on the PR page. In a few minutes you will see Github display the new comment without doing anything yourself. This let's me and other organizers see the resulting changes in a mostly accurate way; without having to pull it down and run hexo.io ourselves.

In fact the December post for the [OKCsharp][] website was done completely in the Github UI. I made a new file and committed it to a new branch in my fork. Sent it as a PR. Waited to see the screenshots looked correct. I noticed an error, made the correction, saw the new screenshots in the PR then merged. All directly from the Github website. Neat.

[automation of hexo.io]:/post/automating-static-sites-with-hexo-io-circleci-and-github-pages/
[website screenshots]:/post/generate-screenshots-of-website-during-build/
[suede-halibut]:https://github.com/davidroberts63/suede-halibut
[Express.js]:http://expressjs.com/
[techlahoma]:http://techlahoma.org/
[router methods]:http://expressjs.com/4x/api.html#router.methods
[CircleCI REST API]:https://circleci.com/docs/api
[JavaScript videos]:https://www.youtube.com/channel/UCO1cgjhGzsSYb1rsB4bFe4Q/videos
[mpjme]:https://twitter.com/mpjme
[Github package]:https://www.npmjs.com/package/github
[Personal access tokens]:https://github.com/settings/tokens
[OKCSharp]:http://okcsharp.net/