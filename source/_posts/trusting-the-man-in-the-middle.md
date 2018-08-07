---
title: Trusting the man in the middle
date: '2018-07-02T12:51:13-05:00'
tags:
  - tls ssl certificate
---
### Python

Specifically, PIP will need to be told about the CA bundle you want to use. There are many ways:

- Via the command line argument
  ```
   pip --cert path/to/cert install somepackagename
  ```
- Via pip's configuration
  ```
   pip config --global set global.cert path/to/cert
  ```
- Via an environment variable named `PIP_CERT`

Python itself, via requests your application makes, depends on the module you are using. A common one is the `requests` module. Set the `REQUESTS_CA_BUNDLE` to the path of your ca.pem file and you are good to go. Remember to reload the environment/profile when you do that though.
   
> Using Azure's Python CLI?
> C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe
