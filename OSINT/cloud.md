# Cloud Storage

> *Source HTB Academy*

Sometimes S3 buckets (AWS), blobs (Azure), cloud storage (GCP), can be accessed without authentication if configured incorrectly.

## Company hosted servers

- `for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done` (subdomain list is a file that we made from previous enumeration) We could find url that looks like cloud storage example `s3-website-us-west-2.amazonaws.com`

## Google search

- `intext:target inurl:amaxonaws.com` AWS
- `intext:target inurl:blob.core.windows.net` Azure

## Other

- We can also look it the source code of the website of our target
- [Domain Glass](https://domain.glass/)
- [Grayhat Warfare](https://buckets.grayhatwarfare.com/)

## Private and Public SSH Keys Leaked

- These websites could also allow us to find Private or public SSH key `id_rsa` or `id_rsa.pub`