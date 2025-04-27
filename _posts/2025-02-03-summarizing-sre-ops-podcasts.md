---           
layout: post
title: Summarizing SRE/Ops Podcasts Using an LLM
date: 2025-02-03 00:00:00 IST
comments: true
categories: podcast sre ops llm
---

## Introduction

There are plenty of good SRE/Ops related podcasts out there. I follow a few of them and listen to episodes whose titles sound interesting. 
The problem with podcasts is that some episodes focus on one topic, and other episodes deal with a host of topics. In between there is
filler and things that are not relevant to the topic but are necessary to carry on a conversation. Spending 30-60 minutes listening to podcasts is not always a great use of time. 

A while ago I decided to create a tool that summarizes podcasts for me using an LLM. If I find the summary interesting enough or there is something that I want to learn about, I go and listen to the entire
episode. Such a tool might be useful to others also, so I made a website for it - [https://www.srenews.info](https://www.srenews.info).

I encourage you to listen to the complete episodes if you find a summary interesting - they are linked from each summary page.

My personal favorites include Google's SRE Prodcast, Incidentally Reliable, and Slight Reliability.

## Architecture

The architecture is pretty simple.

![Podcast Summarizer Architecture](/assets/images/podcast-summary-architecture.png)

Behind the scenes:
- The feed checker is not automatic yet. I run it manually for now. The code runs on Cloudflare workers.
- The checker triggers a Typescript program which fetches Youtube metadata and the transcript. I chose Typescript as Python packages are not available on Cloudflare workers [as of this writing](https://developers.cloudflare.com/workers/languages/python/), otherwise Python is my first choice for such things.
- The transcript is fed into OpenAI which generates a summary.
- The summary and the metadata are used to generate a post based on a Hugo template and pushed to Git.
- Netlify deploys the site automatically on Git push.

So far the only costs I'm incurring for this are for the domain name and OpenAI's API - and it's worth it.
