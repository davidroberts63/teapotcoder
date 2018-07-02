---
title: Trusting the man in the middle
date: '2018-07-02T12:51:13-05:00'
tags:
  - tls ssl certificate
---
### Python

This tells you where Python is looking for certs [get_default_verify_paths](https://docs.python.org/3/library/ssl.html?highlight=ssl_cert_file#ssl.get_default_verify_paths).  
```
python.exe -c "import ssl; print(ssl.get_default_verify_paths())"
```

If the default hasn't been touched you'll see you can use an environment variable named `SSL_CERT_FILE`.  

> Using Azure's Python CLI?
> C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe

