---           
layout: post
title: Monitoring Best Practices - Tracking Service Provider Outages
date: 2023-10-30 08:00:00 IST
comments: true
categories: techops monitoring uptime
---

If you have a SaaS product, chances are you are using one or more public clouds and monitoring services. If you are running 
a development team, a third party likely manages some of your dev tools and collaboration tools. Your service uptime 
will be a function of the uptime of these providers. Some may have an indirect impact - like affecting your ability to push 
bug fixes to production. Others, like DNS, would be directly tied to your product’s uptime. You can take preventive or 
mitigative steps if you get notified about service provider incidents on time.

In this article, I’ll explore this impact and different ways to stay updated. I’ll also share a list of endpoints that you can track.

### The Impact of Service Provider Outages

#### Infrastructure and Hosting Providers
Incidents in DNS, Cloud IaaS, managed services, and CDNs can breach the SLA you have promised to your customers. DNS and 
CDN providers have many layers of redundancy built in - due to their front-facing nature - so outages are rare. When they
happen, though, you would want your systems, rather than a customer, to notify you.

Cloud applications have to be tolerant of faults in the underlying infrastructure. Whether this is true or not for your 
applications, you would want to know about any infrastructure issues immediately.

#### Development Tools
Managed VCS and CI/CD service outages can affect your development cycles. They can also hamper your ability to push 
critical production fixes. Notifications might not help you much here unless you have alternative methods of building 
and deploying. But it still makes sense so that you can let others in your org know when the service is down and back again.

Outages in artifact and container image repositories can impact new deployments. Autoscaling of new pods in environments 
like Kubernetes when new VMs come up will fail, causing service disruption.

#### Communication and Collaboration Tools
Incidents in Slack, Zoom, Google Meet, Microsoft Teams, etc can hamper internal communication. Delayed and dropped messages 
will just confuse your team.

#### Monitoring and Alerting Services
Who monitors the monitor? This question bugs every good TechOps engineer. Your monitoring can be in-house (not a good idea),
a mix of internal and external (the sweet spot?), or completely external (can be costly). When one or more of your external 
monitors goes down, you will want to know. If your paging service is unable to alert on-call folks, you will want to know 
so that alerts are not missed.

#### Business Tools and Other Third-Party APIs
Issues in payment providers and authentication services can lead to loss of revenue and customer trust. Outages in your
marketing tools will affect your planned campaigns.


Sometimes, an incident in a global service might not have affected you yet. It might have affected a subset of geographical
locations. It still makes sense to get alerted so that you can prepare for the worst if it does.

### So, What is the Solution?
Almost all service providers have a public status page. What we want is an automatic way of getting notified when there 
is something new on the page. There are a few ways to do this:

- Check if the provider has an RSS feed for their status. The public status page is a good place to look for this. Pull the
RSS feed into a tool like [IFTTT](https://ifttt.com/){:target="_blank"} and have it deliver notifications to a channel of your choosing 
(Slack, email, text, phone). This is easy to do, but you have to repeat it for each feed.
- Some status pages have direct integration with Slack, email, and text. You don’t need an intermediary like IFTTT if so.
Pages built with [https://status.io/](https://status.io/){:target="_blank"} have this feature out of the box. This takes 
the least effort if your service provider uses it.
- If the status page does not have an RSS feed, there are tools to convert page changes to RSS. You can hook in that feed 
using IFTTT. This is a hit-and-miss affair as any changes in the page structure can break the feed conversion.
- Some services post incident updates on Twitter, but it’s not easy to get notified from one account alone.
- If you don’t want to do this yourself, some paid apps aggregate status from different services and notify you.

#### How does IFTTT work?
IFTTT stands for "if this, then that". It’s an online app for stringing together disparate services. At its most basic, 
it pulls in data from a particular source, checks for a condition, and then performs an action. Examples:
- Pull in data from an API, check for a specific keyword, and send a message to a Slack channel.
- Pull in data from an RSS feed, and send it to a specific phone number via text message.
IFTTT can watch an RSS feed and send a message to your internal Slack channel when there is a new entry in the feed. 
- A new entry is either the start or the end of a new incident.

### The List
I’ve created a [list of status pages and feeds](https://github.com/talonx/service-provider-status-links){:target="_blank"} 
on GitHub - check it out and send me a PR if you want to add to it.

**tldr;** It’s important to monitor your service providers, and there are different ways to do it. Choose the way that works best for you.

Comments? Let me know on Twitter [@talonx](https://twitter.com/talonx){:target="_blank"}

Disclaimer: I have no affiliation with any of the tools mentioned.