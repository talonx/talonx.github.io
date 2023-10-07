---           
layout: post
title: A Twitter bot for Classical Music with Google Cloud Functions
date: 2023-10-07 10:00:46 IST
comments: true
categories: google-cloud-platform, twitter
---

Writing a Twitter bot that just tweets and does nothing more is supposed to be easy. It was not _very_ easy because I ran into a few issues with
authentication, understanding Twitter's new APIs and their capabilities, and some problems with the Google Cloud Scheduler permissions.

### Goals
My goals for the bot were simple
- Automatically publish a tweet on a bit of western classical music trivia once a day.
- Do this with zero hosting or deployment costs.

### Tools and Libraries
Twitter has different authentication models, and for my use case the OAuth 1.0 User Context was appropriate - I needed a Twitter app to perform some action (tweet) on behalf of 
another account. Here is where it can get a little murky - to write a Twitter app you need a Twitter developer account. And since this is a bot, you want another Twitter
account for the bot. So should you create the developer account in your personal Twitter account, or in the bot account?

Turns out this can be a common source of confusion, and has been [clarified](https://twittercommunity.com/t/multiple-bot-accounts/128332/2){:target="_blank"}.

So I used my personal Twitter account as the developer account and created a bot account for the bot. 

Getting the right credentials was a breeze with [twurl](https://github.com/twitter/twurl){:target="_blank"}. Be careful if you are doing this on a shared laptop - twurl stores the credentials
in a .twurlrc file in your home directory in plaintext.

I used [Tweepy](https://github.com/tweepy/tweepy/){:target="_blank"} to interact with the Twitter API. Python is the obvious choice for quick, small projects.

The next question was where to deploy, and also fetch/store the data from. A cloud function was the obvious choice - no builds or artifacts to worry about. It's been a while since I
have worked with AWS, so I chose GCP's Google Cloud Functions. This naturally led me to use a GCP datastore. I settled on Firebase as it seemed easy to use for this - I have no prior experience
with Firebase. I stored the data in JSON format for easy editing in Git and pushed it to Firebase.

Curating the initial tweet data (at least for 10 days, one titbit per day) took some time. All the titbits are hand-curated from various freely available sources. There can be a lot of anectodal 
information when it comes to classical music personages, so it's important to make sure that it's verified as much as possible.

I've been pushing new trivia into Firebase almost every day since then, and it will be at least a couple of months
before I cover it for an entire year. After that, I would have to do one of the following things
- Replace the existing trivia with new.
- Replace this with data for OTD - something significant that happened On This Day.
- Decrease the frequency of tweeting and add media and richer tweets.

### Deployment
Deploying the function into GCF was easy. While setting up a Google Cloud Scheduler run I ran into some authentication issues. Turns out that if the Cloud Scheduler API was enabled in the account
before March 19, 2019, the cloud scheduler service account had to be [added manually](https://cloud.google.com/scheduler/docs/http-target-auth#add){:target="_blank"}. That did not work, so I disabled and enabled the API
and things were fine. 

Total monetary cost involved in deploying this: Zero. 

Most of the time taken was in curating and inserting the data - it's an ongoing process.

The source code is on [GitHub](https://github.com/talonx/classicalbot/){:target="_blank"} and the bot is [live on Twitter](https://twitter.com/classicaltrivia){:target="_blank"}.