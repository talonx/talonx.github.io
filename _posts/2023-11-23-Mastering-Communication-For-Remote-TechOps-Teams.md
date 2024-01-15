---           
layout: post
title: Mastering Communication For Remote TechOps Teams
date: 2023-11-23 00:00:00 IST
comments: true
categories: techops remote-work communication
---

As a backend developer who also built and led TechOps teams in different startups, I've experienced communication challenges that are specific to the way ops and other teams interact with each other. The rise of remote work has added new dimensions to these challenges.

In this article, I will discuss some key things that will help you master communication as a remote techops engineer. I had in mind smaller workplaces while writing this --- like startups, which are unstructured and need to get things done fast. Yet, I think the principles are generally applicable.

![Communicate](/assets/images/remote-communication.png)

**Communicate Your Priorities**
===============================

TechOps folks spend a lot of time firefighting and debugging. Weigh your priorities when faced with many issues. If you have to switch tasks, let people who are waiting on the outcome know about it. Faced with conflicting priorities that all seem important? Ask for help, and get the dependent people involved.

Priorities are fluid. Fixing a deployment failure for a team is important but an LB routing problem that affects a customer takes precedence. I keep a running list of immediate todos in a text file --- use what works for you. In Slack, you can set threads as unread or mark them as remind-me-later. Being on top of things without multitasking is what matters.

**Keep Things Visible**
=======================

The nature of ops is such that a lot of the work we do spans teams and services. Keep teams updated on stuff that's going on. Keep conversations in public chat threads and not in DMs.

Insist --- and I cannot stress this enough --- on using public channels for such updates. Public channels are open to everyone in the org. This helps to build an open culture and avoids "hidden" work. Hidden work is work that nobody knows about --- including your manager and your teammates.

It's also easy to add more people to the conversation later in public threads. If you are chatting with somebody over DM, you have to start a new conversation to add a third person. You end up losing the previous context.

If you are working on something and others are waiting on it, it's best to get on a call.

If you are not on a call, send regular updates in the chat thread.

> *Me: "Disk 1 restored"*
>
> *Me: "Zone 1 is back up, checking connectivity from the other cluster"*
>
> *Me: "Failover to the new zone in progress..."*

If this feels weird and robotic, imagine this:

*You are sitting around a table with others. You go on typing on your laptop while the others are waiting for you to say something. You don't even look at them, let alone speak.*

That's weird, right? It's the physical equivalent of being silent in a Slack thread. The alternative --- to type these updates --- is not weird.

Instant messaging does not make for a great communication medium but it's not going away soon. We can always try to be thoughtful about how we use it.

**Learn to Write Well**
=======================

Being able to write well will set you apart from most of your colleagues. It is possible to develop your writing skills on IM and [email](https://code.deepinspace.net/posts/2014/12/02/Effective-email-communication/) to get your point across concisely and effectively. As Jason Fried and David Heinemeier Hansson wrote in "[Remote --- Office Not Required](https://www.goodreads.com/book/show/17316682-remote)":

> *"Becoming a better writer is entirely possible. Few people are born with an innate talent for writing; most good writers have practiced and studied their way through"*

Practice writing just like any other skill. How do you know you are getting better? Measure the number of times people ask you for clarifications and the number of times you are misunderstood.

**Tailor Your Messaging**
=========================

A lot of your work will involve communicating with varied groups, from dev teams to C-level folks. Knowing how to present to your audience is as important as the work you do.

When starting online discussions, keep all relevant people in the loop. Don't add people to whom you can send a summary later --- it is noise. Use your judgment on who to include.

When responding to incidents, create a public chat thread so others can follow your progress. Summarize technical details in the engineering channel, and tag people or teams if they might face disruption. If there is no common engineering channel, post in your ops channel and tag the team leads. Post the root cause analysis link after it's done.

Post in a global team channel --- with engineering and non-tech folks --- if the incident affects the business in some way. Add an ETA for resolution, if you have it, and the expected impact. If you think it "might" impact the business, but you are not sure, don't post. It just confuses non-tech folks. Knowing the gory details of the incident is important to you and the engineering team, not to anyone else.

Even if you get on a Google Meet, Zoom, or Slack call, post the outcome in whatever tool you use for internal communication. Remember to be "robotic" when it comes to updates.

**Talk About Your Work**
========================

Make your work visible by talking and writing about it. When attempting this you might run into these defeatist thoughts

-   Everyone already knows this

-   What if I say something wrong?

-   I am not comfortable talking about this

-   People will think I am trying to show off

And so on.

The reality is unless you talk about the interesting stuff you are doing, nobody will know about it. A hundred things are always going on in any software dev team.

> *You don't want to be the team or the engineer that people remember only when there's an outage*.

You can talk up anything. The work you do has an impact on the business otherwise you would not be doing it. Don't downplay your work for fear that others will think your work is trivial. Cultivate the skill of being able to talk about the impact of your work. If your company has a data-driven culture, use metrics to support your points.

For example, you can talk about how you are ahead of schedule or completed a migration without incidents. Or post an FYI about something you or your team accomplished. Participate in internal tech talks, and encourage your team members to do the same. If you are uncomfortable talking in front of an audience, then start with writing. Move to giving small talks to your own team first, and then expand to a wider group.

**Be Responsive**
=================

The first thing I tell my new hires is

> **Don't be a black hole.**

Respond to your teammates within a reasonable time. This is often misunderstood, so it needs explanation.

Being responsive to a request is not the same as getting the work done. It is not the same as responding when it's done. It is about keeping the other person updated throughout the process. In remote teams, it can become a pernicious cause of friction if not done thoughtfully.

This conversation between two ops engineers --- whose parents decided to name them after Star Trek characters for some reason --- illustrates the point:

> *Janeway: "Hey Tuvok, B'Elanna told me she sent you the new cluster spec yesterday."*
>
> *Tuvok: "Yes, she did."*
>
> *Janeway: "She just asked me when it will be ready --- she needs it for the demo tomorrow."*
>
> *Tuvok: "Oh, I'll have it up by this evening."*
>
> *Janeway: "Great, did you let her know?"*
>
> *Tuvok: "No, I'll tell her once I'm done."*

Poor B'Elanna --- she was left wondering if she'll ever get her cluster, or if there was a problem with the spec.

This is a contrived example, but it's more common than you think.

Stick to first principles here --- respond on time.

Being responsive is not limited to replying when you are around to look at a problem. It is also about how you react when you are faced with simultaneous problems. Keep all stakeholders in the loop and let them know that you have multiple things to do, which one you are addressing first, and why.

**Have Empathy**
================

The life of a techops engineer can get pretty crazy. Infra rollouts and deployments, firefighting, worrying about all the tech debt you are adding --- and on top of that, people pinging you with random requests.

Take a step back --- because there are no random requests.

Everyone has a job to do --- and all requests are for a reason. If it feels random, it is probably because you don't have enough information about it yet. Get on a call with the person to understand it better, especially if it's somebody you don't interact much with. Maybe it's an automation gap, or you need to update the internal wiki, or it's something the existing tooling does not support. The other person will understand what constraints you have, or that there might be a better approach. In the end, you will have established a better rapport with each other.

Text chat is not good at building such understanding. Slack and IM tools are terrible for nuanced communication. Face-to-face communication, even over a video call, builds trust.

Always remember that your first customer is [your team](https://www.linkedin.com/pulse/your-first-customer-team-hrishikesh-barua/).

**Set Expectations**
====================

It is common for ops teams to keep taking on more and more ad-hoc work while long-term strategic work gets pushed back. In a startup, this is unavoidable. So what's the way out? Spend some extra time building small, common solutions and abstractions that teams can use instead of ad-hoc patches. If you are not in a startup and face this problem, then it's time to hire more people or push back on such requirements.

Clearly set out the ops team's responsibilities. Talk about the tools and services that ops will manage. Make sure everyone knows about it. If you run a platform engineering team the contracts will be clearer. If you don't, then document the contracts as best as you can.

**Document Obsessively**
========================

Your internal wiki must be the starting point for any questions, including those from your team. I am a big fan of wikis. Anything that needs manual work, or needs "tribal" knowledge, or just a starting point for a how-to --- should be in the wiki.

Wikis evolve. Pages become obsolete as old infra dies, things get automated, and new pages are added. Unmaintained wikis can suffer from a variation of the [Broken windows theory](https://en.wikipedia.org/wiki/Broken_windows_theory). They are as good as dead because you don't know which docs are correct anymore.

Conclusion
==========

Mastering remote communication will not only help you become a better techops engineer, but it will also help you become a better colleague and team member.

If you liked this article, you might also like [What Makes a Good TechOps Engineer?](https://code.deepinspace.net/posts/2023/10/26/What-Makes-a-Good-TechOps-Engineer/)

Photo by [Compare Fibre](https://unsplash.com/@comparefibre?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) on [Unsplash](https://unsplash.com/photos/macbook-air-displaying-woman-in-white-shirt-fRGoTJFQAHM?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash).