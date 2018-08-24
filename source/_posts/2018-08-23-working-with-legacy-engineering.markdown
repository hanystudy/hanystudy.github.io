---
layout: post
title: "Working with Legacy Engineering"
date: 2018-08-23 15:04:57 -0400
comments: true
categories:
- engineering
tags:
- legacy code
---

Looking back to the past five years, my common experience for joining a project is always looking like this:

Learning business knowledge -> Revealing technical context -> Launching a new project and instilling good practices into the team to make better software as usual.

While it's not always true for software engineers, I rarely got chance to work on the pre-existing codebase, not to mention a whole legacy engineering. 

Things have changed since I stepped on a new journey. My very first challenge is taking over a legacy engineering. I call it legacy engineering because all I have to care is far more than only the codebase, although it is absolutely focal to tackle with the legacy code. The rest of this article will introduce the steps and thoughts I've taken during this process.

### Communication

A good teacher can at least half the work. Everyone who ever worked in the team can teach and benefit, but the following question is how to learn from people more effectively. My answer is no doubt through good communication. 

For those who are interested, I would suggest some great learnings like [Nonviolent Communication](https://www.nonviolentcommunication.com/) and [Crucial Conversations](https://www.amazon.com/Crucial-Conversations-Talking-Stakes-Second/dp/1469266822). No follow-up discussion since we're not talking about psychology here, but remember it's indeed the first priority soft skill as a professional.

### Documentation

For a hand-over stage, documentation in any form is less effective than a good communication. But it's worthy to keep valuable knowledge and make it more efficient to spread those to broader stakeholders. Again, it always depends on requirement at the moment for determining the scope of what should be documented. My personal inclination is several topics listed as follows:

1. Team specific knowledge that everyone should be aware of.
2. Any confusing/complicated part.
3. The reason for counter-intuitive decisions made by former team members.

It finally leads to three basic documents for me:

1. Feature list (Rough is good).
2. Team engineering practice, like workflow definition and release cycle.
3. Crucial code design.

Of course, the Evernote in hand to prepare for any unknown technologies and skills.

### Codebase

Being familiar with codebase is always the top priority for a newbie, but former topics we just discussed would influence the effectiveness of codebase learning. As software engineering today has adopted many common practices like CI/CD and DevOps, those automation practices can help to    guide people along with the code or script, rather than outdated document and lengthy tutorial.

#### Build (Pipeline)

Build automation is the most matured practice in nowadays software engineering. For a newbie, the build script provides panorama and coarse-grained view of system modularity and dependency, it would be even clearer if the team is maintaining an effective CI pipeline. 

A undoubted fact is that the more matured for build automation, the more efficiency for a newbie to get first wave things done. Otherwise, it would take weeks to set up a local environment by referring to  an far outdated guide and consulting different team members!

#### Test

It's not very common to see "effective tests" in the codebase, not to mention the practice of the whole test pyramid or [sandwich](http://www.hanyi.name/blog/2017/12/17/owl-exploratory-testing/). Automation test is a proven practice to assure quality, sometimes it is promoted to be the specification of the codebase. However, bad practice for automation test also does harm other than keeping high quality.

For a newbie, in most cases, the tests especially the  bad tests cannot help for specification at all, it shows less value than a good naming practice. My personal experience is that the test is not so good for early learning if it's not well organized and properly implemented. That's not saying that it doesn't play vital role to indicate the consistency when any changes happen.

It's usually easy for everyone to see the value of automation test, while it also  makes this practice be one of the most confusing parts in modern software engineering as  lack of deep understanding.

#### Operation

As DevOps is more adopted, it turns to be more necessary for a newbie to understand operation knowledge. Must-have topics include configuration management and specification of cloud platform and core systems.

#### Design (Structural) and Refactor

The core of codebase learning is about design. A design could be regarded as a collection of decisions along the software development. For a newbie, it's more important to concentrate on the crucial design points than knowing every corners.

Although it varies for the different roles, as a backend newbie, the top concern comes into the structural design. To learn software structure,a  good starting point could be any selected feature from both business and technical perspective, it can help dive into the deeper codebase to understand the structure, then forms into whole picture of modularity design.

A good approach is to elaborate design with some tools, CRC even Naked CRC is agiler, but UML is also very cool and common, it again depends on team background for these tools selection. More important is that whichever tool is used, only the leading design rules need to be considered, rather than drawing a full system map. During this process, design pattern may benefit but also cause restriction.

It's not exactly same for design of software structure and logical data model, but the latter could also be learned via any cleaner ways (like E-R diagram), not necessary to dig into physical data model too early unless there is significant reason to do that. 

### Feature Enhancement

I wouldn't suggest making big change or refactor on the legacy codebase which is just taken over unless the change is too small to cause any visible influence. Before doing any work alone, a newbie still needs to learn from core team members about:

1. Architecture Roadmap.
2. Non-functional requirements.
3. Technical debts and roadmap of resolution.

Despite the  following work would be feature enhancement in most cases. I'd take different strategy depends on the potential influence of the change. 

Here I'm not going to discuss the case of a tiny change, that wouldn't be a big deal anyway. But if the change is going to happen on the higher level design rule but not touch leading yet (which usually implies a broader influence across code), I could try to add new features via extensional way (rather than editing as is code and making feature changes directly, methods metioned in [Working effectively with legacy code](https://www.amazon.com/Working-Effectively-Legacy-Robert-Martin-ebook/dp/B005OYHF0A) are good guidance), and extra feature toggles may help for QA and CD.

The worst case is the desired change must happen on leading design rule, which means it won't work with just simple extension, a big change is definitely required. Even in this case, I wouldn't edit any as is code in an early stage, and purely technical refactor without guidance from business requirement is also not a good option. Instead, I'd create a separate package or namespace as the neighbor of the code which is going to die, and naming it "experiment" or anything makes sense. Using least effort to make experiment code work as well as automation test, without breaking any as is feature or code. Once new feature and refactor direction is confirmed, the experiment code can be transformed into formal and replace dead one.

The benefit of this "experimental duplication" is that I can continue looking at two implementations and making a trade-off when thinking of the design for following refactor until the new feature is completed and confirmed. Later on, refactor and clean-up can be done to completely remove the deprecation.

### Conclusion

In this article, we discussed the strategies I would take while working with legacy engineering. Someone calls it reverse engineering to imply the   digestion for an incredibly overdue but complicated legacy codebase or engineering. While in most cases it does not have to make upfront "reverse engineering" to reach critical decision for future, nor do a hasty change on codebase to cause risk. We can certainly take actions to delay that decision making, by mitigating waste and respecting the economic model of our beloved software industry.
