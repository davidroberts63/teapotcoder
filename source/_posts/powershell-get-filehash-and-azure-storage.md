---
title: PowerShell Get-FileHash and Azure Storage
date: '2018-10-09T13:22:32-05:00'
tags:
  - powershell azure storage hash
---
TL;DR

When uploading files to Azure Storage and setting the Content-MD5 property use `[System.Convert]::ToBase64String`. Using `Get-FileHash` or `[System.BitConverter]::ToString` will cause problems; the Azure Storage SDK uses the Base64 format but the BitConverter produces a hex value format. Both methods produce the same hash, but using different formats.

### 
I was messing with a [custom script extension (CSE)](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/custom-script-windows) for a VM; everything was fine until I started automating the uploading of the powershell script for the CSE into Azure blob storage. I decided to set the `Content-MD5` for the blobs so I could keep from uploading same exact files repeatedly (then only upload when it's changed).

I kept getting:
```
Failed to download all specified files. Exiting. Error Message: One or more errors occured.
```
