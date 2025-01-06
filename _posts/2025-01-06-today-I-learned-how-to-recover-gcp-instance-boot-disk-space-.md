---           
layout: post
title: Today I Learned - How to Recover a GCP Instance With 0 Boot Disk Space
date: 2025-01-06 00:00:00 IST
comments: true
categories: today-I-learned
---

I have a GCP instance that I bring up periodically using [instance schedules](https://cloud.google.com/compute/docs/instances/schedule-instance-start-stop) to run database backups. 
My database provider has backups of its own but I have an additional backup in place. The GCP instance on boot runs a user script which:
- Creates a database dump into a temporary file
- tar + gz the dump file
- Uploads it to a secure bucket
  
If this backup fails, I get alerted on Slack.

The Slack alert fired today. I checked the instance and it seemed to have booted up and went down as expected. I booted it up manually and tried to ssh and got a public key error. 
Attempting to ssh using the browser from the GCP cloud console also failed with the same error. 

I enabled serial port logging - and the logs showed that the instance booted up but failed to write the temporary backup files to disk due to lack of space.

Now increasing the boot disk size seemed like one option. However, increasing the disk size from the GCP console just increases the size of the disk and not the partition size. So I needed a way to either increase the partition size or 
delete some files to free up space. But since I could not login to the instance, I could not do either.

The generally prescribed option here is to:
- Create a snapshot of the boot disk
- Create a new disk from the snapshot with a larger size
- Boot with the new disk (or create a new instance with the new disk)

There seemed to be a shortcut - which was to "detach the boot disk" from the instance and attach it to a new temporary instance, and then:
- Boot into the temporary instance
- Delete files from the disk
- Delete the temporary instance
- Reattach the disk to the original instance

This worked.

The reason behind the disk going out of space was that my backup script was not deleting the temporary files after the backup was complete, which I fixed.
