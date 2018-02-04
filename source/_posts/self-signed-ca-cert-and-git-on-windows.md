---
title: Using self signed CA certificates with git on Windows
date: 2016-05-01 21:55:37
tags: windows git tls ssl github
publish: false
---
# DRAFT

Using git internally to access github? Behind a corporate proxy that has an self signed CA cert? Is git complaining about 'SSL certificate problem: unable to get local issuer certificate'? Here ya go.

First, I must thank Philip Kelly for posting an article on this exact situation back in 2014. I also want to say thank you to Alejandro Campos Magencio for the post on getting the certificate chain. I put those two together with the exporting of the certifcate to a file to produce this post.

[Philip Kelley's post](https://blogs.msdn.microsoft.com/phkelley/2014/01/20/adding-a-corporate-or-self-signed-certificate-authority-to-git-exes-store/)

[Alejandro Campos Magencio' post](https://blogs.msdn.microsoft.com/alejacma/2011/06/21/how-to-verify-validity-of-certificates-with-net/)

Philip's post works but I wanted a way to do it from Powershell so I could very easily share the solution with others.

```powershell
CLEAR

Write-Host "Getting certificate for internal site"

$request = [Net.WebRequest]::Create("https://internalsite.supercorp")
$request.GetResponse() | Out-Null
$cert = $request.ServicePoint.Certificate
$bytes = $cert.Export([Security.Cryptography.X509Certificates.X509ContentType]::Cert)
Set-Content -value $bytes -encoding byte -path "internalsite.supercorp.cer"

Write-Host "Getting chain for CA cert"
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)

$root = $chain.ChainElements | Select -Last 1

$bytes = $root.Certificate.Export([Security.Cryptography.X509Certificates.X509ContentType]::Cert)
Set-Content -value $bytes -encoding byte -path "supercorp.ca.cer"
```
