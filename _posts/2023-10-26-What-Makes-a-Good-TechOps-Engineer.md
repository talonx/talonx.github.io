---           
layout: post
title: What Makes a Good TechOps Engineer?
date: 2023-10-26 17:00:00 IST
comments: true
categories: techops, opinion
---

I’ve been in backend development for most of my career. Some part of it was spent doubling up and leading the Ops teams, including
a short stint of doing just Ops. This is a rambling list of attributes that I would value in a TechOps engineer. The best engineers I
have worked with - and not just in Ops - had most of these to various degrees.

Warning: opinions ahead.

### Wait, So What Do You Mean by a “TechOps Engineer”?

The definition of "DevOps" reached a saturation point long ago. What started as a cultural movement is now a job description 
and a designation. "DevOps" has brought about immense changes in the way we deliver software in both approach and tools.

Personally, it has brought about the helpless realization that in spite of it being misappropriated to represent everything
from a tool category to a job title, the term “DevOps” is the quickest and easiest way to convey this set of changes, this way of doing things, 
to folks in the industry.

I still shy away from using the term “DevOps engineer”. I prefer “TechOps engineer” (TE). For me, it encompasses the roles of
ops/cloud/infra engineers and also has some overlap with SRE.


### Good TechOps Engineers

#### Are Curious

TechOps engineers are curious about everything. They are curious about minute things that others might downplay or dismiss. 
This obsession with small details can often lead to missing the forest for the trees. At the same time they have the ability to hold the big picture in their heads.

#### Have a Secure Mindset

They have an innate sense of what is secure, and what is not. Good developers can detect code smells; good TechOps engineers (GTEs) 
can detect security smells from a mile away. Leave your laptop unlocked and they will extrapolate security disaster scenarios you could not have imagined. 
I remember the time a person in my team was using an Ubuntu image screenshot tool for a customer’s internal dashboard which would auto-upload them 
to imgur.com (not good). And then there was another who was using a public Postman request URL, complete with auth tokens, and sharing it with their team
(again, not good). It just struck me that I remember the negative examples here, and none of the positive ones where somebody successfully prevented a possible security incident.

#### Are Comfortable with Complexity

Any infrastructure gets complex over time. Technical debt accrues like mold on the wall. Infrastructure becomes a soup of special cases,
(un)documented manual changes, and legacy setups that we cannot update. And all this might coexist with a Terraformed cluster. Some distributed system components 
somewhere might communicate in weird ways and lead to consistency issues. GTEs have the ability to understand and process complex topologies in their heads.

#### Get Along With Others

True for everyone in a team, but more so for TEs. TEs are in charge of systems used by many teams. Interacting with different people is inevitable.

#### Are Obsessive Documenters and Sharers

The internal wiki is the GTE’s best friend. They ensure it’s all there for their present and future team members. An out of date wiki page
is something to be corrected soon. “Is it wikified?” - is the question I’ve seen them ask and pursue at the end of many troubleshooting sessions.

#### Know Their Audience

Developers form the majority in most teams. TEs (or similar roles) are often seen as doing something arcane. GTEs can talk about
what they are doing from the point of view of devs (or VPs, or CXOs) and connect the dots across the entire tech stack from infra to browser.

#### Are Good at Reading and Understanding Technical Docs

This seems like an obvious one - but the number of people who cannot do so has surprised me many times. Sometimes it’s all in the man pages,
or in the API docs, or the command line options’ help.

#### Love Boring Tech, But Open to Adopting Shiny New Tech

Boring is safe. Boring is stable. If it does the job, why bring in something new? Especially when it's something that can impact
many systems. But when there is an opportunity for significant improvement, GTEs can do a cost-benefit analysis of switching to the
new thing complete with a roadmap and potential pitfalls.

#### Are Responsive

TEs are usually firefighting or debugging issues. Responsiveness is not limited to responding when they are available to look at a problem. 
They might be busy with something and another problem arrives. A GTE would let people know what they are doing. They weigh priorities in the face 
of many issues, and switch to other problems if necessary, or pull in somebody else if they are available. Being on top of things without multitasking 
is the key here. Multitasking usually ends in mistakes unless the tasks are simple. Responsiveness is so important that I make it a part of 
my job requirements. When team communication is over Zoom or Slack this is even more crucial - not having this will make people wonder if they are talking to a black hole.

#### Know Their Tools

Every GTE has a toolkit of their favorite tools - and they are competent at using them. Text editor, debugging tools - it 
does not matter which specific one you use as long as you can use it well.

#### Are Fast Typers

This does not need explanation.

#### Have a Desire to Keep Improving Things

Every GTE I have worked with had an endless list of things that we could improve. We cannot avoid tech debt - the best we can do is track it.

#### Are Generalists with Specializations

GTEs are jacks of all trades, masters of some. The more different ideas you are exposed to, the richer your mental problem solving framework becomes.

#### Have a Sense of Cleanliness

GTEs are bothered when something is not as clean as it can be. Consistency is an obsession with them, and at the same time they understand that perfect is the enemy of good.

#### Are Independent Movers

GTEs can explore and push ahead with an idea on their own, and are confident enough to explore and make changes to complex systems, pursuing it over weeks or months.

#### Do Not Fear Friday Deployments

…because they have automated it and it’s easy to rollback. Even if you don’t have continuous deployment (CD)  in place, GTEs want to make the 
deploy and rollback processes as easy as possible. As far as I can remember, [IMVU](http://timothyfitz.com/2009/02/10/continuous-deployment-at-imvu-doing-the-impossible-fifty-times-a-day/)
started this 14 years ago, and we have come a long way since then in the industry.

#### Are a Little Paranoid

No level of infrastructure automation or security scans can convince a GTE that everything will go well. This keeps them on their toes 
and can bring out unforeseen issues. The downside is this can also create resistance to change.

Comments? Let me know on Twitter [@talonx](https://twitter.com/talonx){:target="_blank"}
