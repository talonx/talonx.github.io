---           
layout: post
title: Today I Learned - Disabling Object Listing for Google Storage Buckets with Public Access 
date: 2024-06-22 00:00:00 IST
comments: true
categories: today-I-learned google-cloud-platform iam
---

Using a Google Cloud Storage (GCS) bucket for static storage is a very easy way to serve static content over HTTPS. For this to work,
public access has to be enabled on the bucket's objects. The access should be read only at the public level and can be set
using one of Google IAM's predefined roles. 

At first glance, the role for this would seem to be `Storage Object Viewer`, and that's what I went with when setting up a bucket 
recently to serve images. This role though also exposes the bucket contents as an XML, which is not something you want.

It turns out that the appropriate role is `Storage Legacy Object Reader`. The difference between the roles can be seen
in their permissions.

Storage Object Viewer has both list and get permissions:

![Storage Object Viewer](/assets/images/viewer-gcs.png)

whereas Storage Legacy Object Reader has just a get permission:

![Storage Legacy Object Reader](/assets/images/legacy-gcs.png)

Thank you for reading, and do reach out via comments or on [Twitter](https://twitter.com/talonx){:target="_blank"} if you want to chat or share your thoughts.