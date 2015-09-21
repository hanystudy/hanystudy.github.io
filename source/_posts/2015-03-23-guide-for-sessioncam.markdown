---
layout: post
title: "Sessioncam: Introduction&Principle"
date: 2015-03-23 23:43:49 +0800
comments: true
categories: 
- 天选之作
- web
tags:
- sessioncam
- user experience
---

Recently we used an online user session recording service in one of our project, which caused my interests and push me to do some spike on such tool —— sessioncam. In this article, I will give an introduction as simple as I can, then try to dig deep into its design and implementation.

### 1.What is sessioncam in one sentence?

SessionCam is a leading “software as a service” session replay product used by clients to record and replay website sessions. you can visit http://www.sessioncam.com/ for more detail.

### 2.Basic features list about sessioncam.

#### i.Session replay
Almost any behaviour of customers will be recorded and can be replayed by developers, including mouse clicks, movements, scrolling, form inputs, or any other actions on page, similar to specific actions on mobile devices. Developers can replay the recordings like playing videos by their sessioncam account with various speed.

#### ii.Heatmaps
User can generate website heatmaps for mouse movement(M), mouse clicks(C), page scrolling(S) and browser attention(A). 

#### iii.Funnels, Form Analytics, Field Drop-Off
A kind of analytics tool to filter out useless data, improve conversion through assessing drop-off of users behaviour, such as page accessing or form inputing.

#### iv.API and integration
User can integrate sessioncam with their own analytics tool, such as Google Analytics, Campaign Monitor, CheetahMail and Olark.

### 3.How to start?

You must register an account on sessioncam http://www.sessioncam.com/plans/ , there are also free plan http://www.sessioncam.com/free-account-request/ , with limited page count per month.

About which plan to choose, from our experience you can hardly use non-enterprise edition if all site visitors are planned to be recorded. Therefore, it depends on the scale of your site. If you only have hundreds users and each users will take hundreds page views on your site per month, that means medium edition is better for your situation.

There are many ways to deploy sessioncam to your website, most of them are very easy work with only a few javascript needed on your page. Refer to https://help.sessioncam.com/hc/en-gb/articles/200863126-Adding-SessionCam-to-your-website .

Login to your sessioncam account console and turn on recording, https://console.sessioncam.com/Dashboard/Manage/HostnameManagement.aspx?accountAdmin sessioncam will start working.

### 4. How does it work?

#### Step 1. IO initialisation
Reading and executing script from //d2oh4tlt9mrke9.cloudfront.net/Record/js/sessioncam.recorder.js, through which it will request for https://ws.sessioncam.com/Record/config.aspx to initialise a sessionCamRecorder object, with tracking configurations from sessioncam server.

#### Step 2. Run

(Please refer to sessioncam.recorder.js when you reading this part)

Sessioncam will capture and clone the whole document inner html at line 5920(function SessionCamRecorder.prototype.ml), during this a pre-processing job should be done at line 6568. Below shows basic workflow:

 a. Replace script, object, image, or any useless target element with short strings. The processed document content will be kept in SessionCamRecorder.hK.

 b. Check whether sessionCamDebug is opened .

 c. Initialise data structure which stores form data at line 5847.

 d. Config for mobile device.

 e. Check XHttpRequest configuration, if disabled by browser it will try swfhttprequest.

 f. Bind any document interactive events in sessionCamRecorder.cU, line 7631.

 g. Once any event triggered, sessioncam will collect event data and push them into IO pool, in SessionCamRecorder.prototype.gG, line 7279.

 h. Looping scan all doms of page per 250ms, record any dom and its xpath value who is different from 250ms before into IO pool. line 6855-6878.

 i. Looping check IO pool and send existed data flow by time to sessioncam server, in SessionCamRecorder.prototype.jM, line 8106.

 j. Send data to sessioncam server per second, in SessionCamRecorder.prototype.lh, line 8584.

#### Step 3. Replay
According to step 2, sessioncam almost collects all the user's browser data at milliseconds level, it's easy to replay the session by time flow. Actually, for session replay there is an iframe element which always figuring to remote getpage.aspx, with parameters like PageId, SessionId, starttime, EventId, HostnameId, etc. Javascript can play this iframe just as animation easily.

### 5.Miscellaneous

Sessioncam identify data only by website domain, which means you must register sessioncam account by a domain, and once such domain has been taken, new account can not set same domain unless the first user permits it.

Session data may contain some user privacy data, but sessioncam will ignore most common item like password, credentials. But admin can still config some privacy fields to be ignored.