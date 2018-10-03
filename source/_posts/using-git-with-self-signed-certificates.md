---
title: Using git with self signed certificates
date: '2018-10-03T14:41:16-05:00'
tags:
  - git tls self-signed
---
Using git internally to access an external git repository? Behind a corporate proxy that has an self signed CA cert? Is git complaining about 'SSL certificate problem: unable to get local issuer certificate'? Here ya go.

High level:
1. Get the self-signed cert in a base 64 encoded file.
1. git config http.sslCAInfo "path/to/that/file.cer"


>>>
You will hear a lot about setting `http.sslVerify` to false. **Please** don't do this. If you leave the environment having the self-signed cert then git will not be verifying **any** TLS/SSL traffic, opening you up to man in the middle attacks.
>>>

#### Detailed with scripts for Windows (I'll work on Linux later).
You need to get the self-signed certificate in a base 64 encoded file. There are a couple ways to do this depending on your situation.

##### On Windows when the certificate is only available from the remote git server itself.
```powershell
# powershell
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

##### On Windows when the certificate is in the Windows cert store.
This may be typical for corporate environments. Easy to tell, if git complains about self-signed cert but your Chrome or Edge browser doesn't complain then it's likely the cert is already on your computer.

```powershell
# powershell
$rootCA = dir Cert:\LocalMachine\Root | Where DnsNameList -Contains "NAME OF YOUR ROOT CA CERT" | Select -First 1
$rootCA = @("-----BEGIN CERTIFICATE-----",[System.Convert]::ToBase64String($rootCA.RawData),"-----END CERTIFICATE-----")
$rootCAPath = "$Home\where-you-want-the-file-ca.cer"
$rootCA | Out-File $rootCAPath -Encoding ascii
```
The `Where` above may need to be modified to find the appropriate cert in your cert store.

### Tell git to trust that certificate.
```
git config http.sslCAInfo "path-to-file-above.cer"
```


** Special thanks **

I must thank Philip Kelly for posting an article on this exact situation back in 2014. I also want to say thank you to Alejandro Campos Magencio for the post on getting the certificate chain. I put those two together with the exporting of the certifcate to a file to produce this post.

[Philip Kelley's post](https://blogs.msdn.microsoft.com/phkelley/2014/01/20/adding-a-corporate-or-self-signed-certificate-authority-to-git-exes-store/)

[Alejandro Campos Magencio' post](https://blogs.msdn.microsoft.com/alejacma/2011/06/21/how-to-verify-validity-of-certificates-with-net/)
