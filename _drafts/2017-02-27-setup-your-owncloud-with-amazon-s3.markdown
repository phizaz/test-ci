---
layout: post
title: Setup your ownCloud with Amazon S3
---
https://hub.docker.com/r/l3iggs/owncloud/
https://github.com/greyltc/docker-owncloud

### Setup the firewall

22, 80, 443

### ownCloud client

They provide native clients for every platform you could imagine ranges from Windows to linux to your phones, even though, android app is not really featureful and not free, but hey it's not expensive and at least can get the job done... may be not graciously.

I'm testing the Linux client right now, actually, I expected it to be faster ... may be this is just the initial run, I have confirmed this that the discovery part will only be slow for the first time. So, I'll give it a little bit more time.

### Of the closing ownClound, and the NextCloud

you might have heard the news, ownCloud seems to be no more ... there is a fork called **NextCloud** pretty much by the same people (?), I'm not betting on any side right now. I know only that who ever wins I'll go that way. For now, I'll give ownCloud a try.

### Using volumes with your ownCloud container

https://github.com/greyltc/docker-owncloud/wiki/Using-volumes-with-your-ownCloud-container

If having problems with permissions: https://github.com/greyltc/docker-owncloud/wiki/Notes-on-resolving-permissions-problems

Why not just use the docker's volumes ? 


File `/usr/sbin/setup-apache-ssl-key`

Line

```
certbot --text --debug --agree-tos --email ${EMAIL} --webroot -w /srv/http/ -d ${HOSTNAME} certonly
```

it should be `/var/lib/letsencrypt/` instead because of the file `/etc/httpd/conf/extra/httpd-acme.conf` says so


the file:

```
Alias /.well-known/acme-challenge/ "/var/lib/letsencrypt/.well-known/acme-challenge/"
<Directory "/var/lib/letsencrypt/">
    AllowOverride None
    Options MultiViews Indexes SymLinksIfOwnerMatch IncludesNoExec
    Require method GET POST OPTIONS
</Directory>

```

### Of Aamazon S3

They currently offer three tiers of storage, standard, infrequent access - standard, and glacier.

While standard and IA - standard are designed to offer quick data access, the glacie isn't, it's cannot store something you want to have access immedietly at your wish.

However, amazon (might) have a set of tools that will ease out and optimize the use of these three kind of storage:

From: https://aws.amazon.com/s3/pricing/
> You can define data lifecycle policies that move data between Amazon S3 storage classes over time. For example, you could store freshly uploaded data using the Standard storage class, move it to Standard – IA 30 days after it has been uploaded, and then to Amazon Glacier after another 60 days have gone by.

For now, I think I'll go wtih the standard one, let's see if it's ever fast enough for the task.


Understanding AWS security types: http://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html

Munaging access keys: http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html

create IAM user: http://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console

Start with the IAM console: https://console.aws.amazon.com/iam/home?#/users$new?step=details 

with Amazon S3 Full Access 


### Of the Cost

I have run it for couple days ... I found that there are far too many http requests to the S3 causing the bill to go up pretty quickly ... I wasn't prepared for this, so I simply gave up. I'll just go back to use the traditional cloud drive. 

But, for the sake of my endeavors and all the efforts I have put on the trial-and-error, I will put it on the internet might it shed light to somebody's darkness, I'll deem it to be worthy :D
