---
slug: why-appish
title: Why Appish? The MVP problem
authors:
  - haxwell
tags: [pre-launch-stage]
---
NOTE: This blog post is a work in progress.

The good thing about coding is that you can build whatever app you can imagine. But the bad thing about coding is that you can build whatever app you can imagine. 

I've spent the past 2.5 years trying to build the next big thing. Building something new always brings the risk of building something nobody wants- and I did this many times. I learned the hard way that I should have built the smallest working version of my big idea and put it in front of customers to see if it would be useful to them. The smallest working version of a product is usually called an MVP (Minimum Viable Product). The point of the MVP is to test out your idea in the real world as early as possible to avoid wasting your time and effort on a bad idea.

I actually first learned about the MVP quite early from Eric Ries' The Lean Startup. However it's easier said than done. I've built many MVPs that took way longer than {the weekend project}, and still didn't pay off. From my experience, the main issues that make building an app quickly difficult are ... I'll walk through the solutions I've found to lead you to how I ended up deciding that Appish would be the best solution.

## Build a webapp

Thanks to the great web technologies we have today, we can build web-apps quicker than ever. Choose a hassle free javascript framework like Svelte, throw in quick styling using Tailwind CSS and DaisyUI. You can even pull in Firebase so you can focus on a single codebase instead of two. Overall, building a webapp with the right technologies is your best bet for building an app quickly.

However, building a webapp has some issues and limitations too.

### Webapps problem 1: The naming nightmare

A webapp is an app-like website. Every website needs a domain name(a ".com" name). Here is how your average person visits a website for the first time: he opens his browser and types the name of the website. If the website has been around for long enough then Google will help him find the website he's looking for. But if the website is new then he can only get to the website through its exact domain name. My point is that the domain name matters.

If you've gone domain name shopping then you know that it feels like all the good names are taken. Sure, there's only so many names to go around and the internet has been around for quite a while now. But the main culprit here is this evil business known as domain name parking: heartless men and women buy up the good domain names with no intention of using them. Then they wait to sell it to someone who actually needs it for 100x the normal price. It's dispicable! If you're like me then you can't afford to buy a parked domain, but unless you're filthy rich I'd advise against buying one. That's because making a big investment early on will subject you to "loss aversion", making it harder to dump your idea later on if it proves to be a bad idea after all.

### Webapps problem 2: Multiplatform pains

When I first got into web development, I fell in love with the "write once run everywhere" promise- after all you can access the web from any of your devices, right? Well apparently it's not that simple. Everything works well enough on desktop, but the cracks begin to show when you start targeting browsers on smartphones. (TBC)

### Webapps problem 3: No push notifications

No push notifications unless you build a PWA(which [has limited support on iOS](https://firt.dev/notes/pwa-ios/)), but then you're opening yourself up to a lot more work. Also, if apple has it their way, PWA's will never feel like real apps on iOS.

## Build on a cross-platform framework

You can build a cross-platform mobile app using Flutter and react-native, but there's no escaping the admin of posting your app to the App Store and Google Play store. I was shocked the first time I posted an app to the app store at how much work I had to put into it, from legal stuff, to making an attractive app-store listing. Not to mention you'll have to wait for about a week before the platforms approve your app. I also found that knowing my app will recieve permanent reviews from users on the app store made messed up my "mvp mindset" by making it more difficult for me to avoid spending more time making the app perfect.

## Just don't build at all (use a landing page instead)

The other option I've heard of is to just build a landing page. I think this is a brilliant idea if you can do it right. That's kind of what I'm doing here after all: discussing the problem and my proposed solution to see if it resonates with potential customers. I think this is the best first step. It can be done quickly using a landing page builder and it forces you to think about important questions like "do I really understand the problem", "does anyone care about solving this problem", "am I able to efficiently reach those who care". But there's still a second part of the mvp that still needs to be addressed which is about what the best approach to the solution is, technically speaking. (give the example of asambe)

## A spark of hope: (Telegram) bots

Telegram is a messaging platform with a beautiful twist: bots. Developers can build a script that acts like a virtual person that users can chat with to get all sorts of things done. If you're not familiar with Telegram then you can just think of it as a Discord competitor.

When I first discovered Telegram bots, I thought it would be the perfect tool to build out MVPs to test out my ideas. This is because:
- There's no graphical UI development because it's mainly just text
- It's cross platform
- users are authenticated automatically
- It offers push notifications out of the box

However, it's difficult to build a decent user experience using text only - especially when your app has any forms the user must fill in. The solution I came up for the forms problem was sending the user a link to a separate website with the form in it. To Telegram's credit, they've been steadily adding features like buttons to improve the user experience. More recently, they even introduced [Telegram webapps](https://core.telegram.org/bots/webapps) which open up a web page right in the Telegram app. I think it's generally an amazing feature, but not so much for the sake of building an MVP since it reintroduces the issues that come with building a webapp.

Overall this has been the best way to build an MVP that I know. But it got me thinking: "shouldn't there be an even better way to do this?"

## Enter Appish to save the day

I wanted to come up with a way to build an MVP that has all the pros I discovered in Telegram bots, but without the cons. Appish is the answer. It's a platform that's like the bots feature of these major messaging platforms but with two key differences
1. **Bots are first class citizens**: which means Appish is an app whose main concern is connecting people with bots, not people with people. Among other things, this means that if you build a bot on Appish, the relationship between your bot and your user isn't tied to the relationship of the user with the messaging app, which can be rocky at times. For example I've had friends who've deleted apps like WhatsApp because of various social issues, some of them quite intense. This also leads to the second difference...
2. **A UI optimized for blissfully interacting with bots**. The way messaging platforms are designed is made to mirror the way people conversate. We freely send each other words back and forth. On the other hand a person speaking to a bot is a different story because a bot will have stricter parameters for what it's expecting the user to say, and it needs to tell the user what these expecations are. On Appish, this fact will be built into the platform itself. (TODO: link to the UI design of Appish here)

We'll build Appish to be a next-gen bots platform. It'll be a way for developers to pursure their most daring ideas as quickly as possible. I also see it being a great solution for building apps that have a very small audience. Or even a great first step for people learning to code to build something usable more quickly. I'm head over heels for Appish and it doesn't even exist yet!