---
layout: post
title: Essentials for monitoring and releasing your app
date: 2016-10-18 18:32:31.000000000 +13:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories: [Mobile, Testing]
tags: [Mobile, Production, Testing]
meta:
  _edit_last: '1'
  mytory_md_path: ''
  mytory_md_text: ''
  mytory_md_mode: url
  mashsb_jsonshares: '{"total":0,"error":"","facebook_shares":0}'
  mashsb_shares: '0'
  mashsb_timestamp: '1590798087'
  mashsb_shorturl: http://www.codingwithsam.com/essentials-for-monitoring-and-releasing-your-app/
  dsq_thread_id: '6186507167'
  _yoast_wpseo_content_score: '30'
  mashsb_twitter_handle: willsam100
  _yoast_wpseo_primary_category: ''
author:
  login: sam
  email: willsam100@gmail.com
  display_name: Sam Williams
  first_name: Sam
  last_name: Williams
permalink: "/2016/10/18/essentials-for-monitoring-and-releasing-your-app/"
---

Getting your on app onto the store can bring up many feelings. On the one hand, all the fun stuff of building the app is done (for this round only), and seeing the app being used by customers is a great joy.

That joy might be short-lived though, shortly after a few crashes come in, some bad reviews, and then finally the massive red bug where you find you screwed it up completely and now your ducking for cover. FP is fantastic, but will only help reduce bugs in your code, not the server, framework, libraries and OS the app is using.
To avoid all the disasters lets go over a few processes that you can implement, and eventually automate, to keep the joy and avoid sadness.

## Force upgrade API
A force upgrade API is a single API that is called every time your app starts (or appropriate time that it occurs regularly), and checks if there is an upgrade that the user must be download, forcing them to upgrade to latest version. This will completely stop the user from using the app. You might be wondering why you would want to create such a bad experience for the users, and I agree that you don’t. This is a safeguard measure, in the event that there is a serious bug in the app and needs to be disabled. I.e Imagine that the app gets stuck in a loop [somehow] and keeps calling the backed constantly, impacting usability for others, and raking up the bill for the server. There are obvious variants of this API, i.e. disabling the app to stop those using the app on the current(which might be best used first in the example mentioned), and also letting users know of possible updates but not forcing them to update if they don’t want to.

## Crash Reporting
Crash reporting adds a 3rd party SDK to your app, that then captures and reports (via web dashboard) useful information about why your app crashed. The crash can be annotated with additional information that you as a developer would find useful (all that state and variables on a particular screen), saving you less headbanging and faster bug fixing. There are many different providers each with slightly different features and service plans. A slight variant of crash reporting is analytics which allows you as a developer to code events and user actions into the app. These again are reporting to a dashboard allowing you to gain insight into how users and interacting with your app. HockeyApp is supported by Xamarin (and also owned by Microsoft) and would be a great place to start.

## 1.0 is out – onto to 2.0
With these two items in place, it’s now a little safer to release that first version of your app out, 1.0, the one you spent your blood sweat and tears on. On with version two and more features, until release time comes again. Now, you not only do you need to release the new stuff but also not break the old stuff. Panic sets in…. You need regression testing

## Regression Testing – Manually
Regression testing is the process of going through the app and checking that all functionality in the old app, is still available and working in the new. This should be a clearly defined release process and must not be skipped. Find a bug after testing, put the fix in and start again from the beginning (I’ve seen bugs released into production due to improper regressions testing. Don’t assume that ‘small’ change can’t break your app entirely; it can). Regression testing is time-consuming and super boring. To top it all off, as humans we’re not likely to remember every button we pressed or to actually do this. When it’s Friday afternoon and weekend is calling….”I’m sure I can skip these last few tests”. Fortunately, there is a better way.

## The better way
Since we as developers are among the laziest (this is a good thing as Bill Gates can attest http://quoteinvestigator.com/2014/02/26/lazy-job/), we can automate some of this. Computers are great at repeating tasks and checking things match; meet UI testing. Both iOS and Android supporting automated UI testing, and there are also many 3rd party libraries to help with this including UITest for Xamarin. It’s not always possible to automate everything (though that would be great), but automating both the core functionality of your app (a chat with your team about what the ‘core’ will be required) and the really boring parts will free up a lot of your time. There is a downside to UI testing, which is if they aren’t run regularly and maintained they will become fragile, difficult to maintain and break. Run often, and always check, it might just be a Samsung update that failed test, or it could be saving you from coding a catastrophic rabbit hole, wasting all your time. As a side note, if you find you're forgetting to run these tests, automate that too – there are several solutions for this called Continuous Integration (CI) services/frameworks.

## The leftovers
UI tests are great, but there are a few things that they can’t pull off very well. The first of these is the app upgrade. This is an important test, that shouldn’t be skipped as it will test that all-important database migration (unless your one of the cool kids using a NoSQL DB in the app: leave a comment if you are). After weeks or months of coding away, you barely even remembered that you renamed that all-important field on your most important ORM model!

The second manual test is checking the app still looks good for a human.UI tests can click buttons, but they can’t ‘see’ the screen and find aesthetic errors that sometimes pop up after a change to the layout files (I'm looking at you Android). Fortunately, this is not too hard, as just involves going over the app and making sure that everything still fits on the screen and text can be read. Attentional to detail is key; observing that those animations are timed to perfection.
Finally, finish off the manual testing with exploratory testing. UI tests will always push the buttons in the same order, the idea here is to push them out of order and see what the app does i.e. pushing buttons and trying to do things that your dumbest users are guaranteed to do! This is also where a tester is really helpful (assuming your company hires them). It’s hard for us as developers to not press the buttons as we coded it, testers think more like users and will break your app in ways that will always amaze you.

As a side note, adding all these things in (API, crash reporting, analytics, UI tests) as early as possible is better. Remember your tester is still a human, so they are going to make mistakes or forget what buttons they pressed. Having the tools in place to replay what they did will be a huge productivity boost. (This will also help fix the cases where only one person is having a problem and you need to figure out what is different about their case).

## 3.0: Cutting another release
From API keys, profiles, certificates, there are many things that need to be configured to get a release build right. For the first version, doing this by hand could be ok since you need to setup up accounts anyway, but not for subsequent releases. Be lazy, and automate this too. There are build tools that once set up and can make this as simple and button click, vary the amount of automation you require with your budget and expected release frequency.

## Over and out
Releasing an app (or any piece of software) always gets the blood pumping, but following these processes and practices will help to reduce the heart rate so you can live to eat more beer and pizza.
P.S leave a comment if you have an interesting stories/lessons of updates gone wrong
