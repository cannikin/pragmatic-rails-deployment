# Introduction

Hello, world! The purpose of this book is to get your Ruby on Rails web app up and running in a free, fast, scalable environment. Specifically Amazon Web Services and its awesome suite of virtual servers, databases, file storage and other cloud infrastructure bits.

This book is geared towards the developer that has never deployed to production before, but still applies to a battle-hardened self-made dev ops engineer who just hasn't taken the plunge to AWS yet. Even if you have deployed to AWS chances are you'll pick up some new tricks in the following pages.

## Why I'm writing this book

I think Infrastructure-as-a-Service, and AWS in particular, is one of the most profound changes in technology since the internet itself. Suddenly everyone has access to world-class hardware for literally pennies per hour. But like any new technology it's still somewhat like the wild, wild west: everyone has their own way to do things and conflicting information abounds.

Then there's the _if you don't use this new tool you'll regret it_ advice and _how-the-big-guys-do-it_ blog posts. People get caught up in the hype when Facebook discusses their platform and how they deploy. When others blog and  cover these posts would declare "if you're not using Facebook's new deploy tool then your app isn't **web scale**!" But honestly, is your current project going to need to scale to Facebook levels in the near future? Probably not. Save yourself days and weeks of struggling to learn the latest and greatest deploy tools that were written by and for people with much different problems to solve than you have (or may ever have).

What I'm presenting in this book is a no-nonsense approach to getting your web app online with a minimum of ceremony and complexity. Does [Chef](http://chef.io) have it's place? Absolutely. But Chef's learning curve is like walking up to the base of El Capitan. Unless you're deploying and maintaining dozens or hundreds of servers (like Facebook) I don't think that learning curve is worth it just to get your brand new app (with zero users) available to the world. As a former collegue was fond of saying: the juice isn't worth the squeeze. (Don't feel bad if you don't know what Chef is yet. We'll be discussing that and many more common, but at times unnecessary, deployment tools.)

That being said, your app should be prepared for reasonable growth and disaster recover:

* One of your app servers suddenly dies
* A junior developer accidentally runs `DELETE FROM users;` against production
* Your launch makes it to the front page of Hacker News
* An image on your homepage suddenly becomes the [next great meme](http://cdn.meme.am/instances/500x/17349069.jpg)

Your site must remain available and serving users with a minimum of downtime (ideally none). The setup and deployment I discuss will keep you up in all of these scenarios, and more. 

W> High availability can become an adventure all on its own. You may start thinking up increasingly unlikely disaster scenarios (the [transatlantic communcations cables](http://en.wikipedia.org/wiki/Transatlantic_communications_cable) are severed by a rogue submarine at the same time a zombie outbreak hits the east coast of the United States!) and come up with more and more creative solutions keep your site up. Keeping your site available in the face of this particular combination of unlikey events is actually possible within the AWS infrastructure! However, I won't have the room in this book to discuss the social and political ramifications of the undead and their insatiable hunger for brains. Sorry.

So, this book is not claiming to be the One True Way™ to deploy your app. More like A Way that Works Great in Many Real World Scenarios™.

## Why you should trust me

I've been a professional web developer and designer since 1998. 

I've been deploying RoR applications in production since 2006 and have lived through the evolution of dedicated to virtual servers, FastCGI to Passenger to Mongrel to Thin to Unicorn, self to cloud hosting.

I've been in charge of maintaining apps with one user (myself) to apps serving millions.

I'm a full-time contract Web Developer (Software Engineer, Computer Programmer, pick your title) and a smattering of Dev Ops usually comes with the territory. I've found that when you start with a solid infrastructure setup then the dev ops part of the job takes care of itself for the most part.

I've developed this deployment strategy over many years of hard-won experience deploying to bare metal and then eventually AWS beginning in 2008. Once I found AWS I haven't looked back.

## What about Heroku?

[Heroku](http://heroku.com) is an amazing service and changed how sites are deployed and maintained forever. If you're developing an app and just need to get it live quickly, Heroku is tough to beat. The AWS deployment you'll learn about in this book may take a day or more the first time you try it (but will probably be reduced to an hour or two once you get the hang of it). Heroku can be deployed in _minutes_, even your first deploy. If you need to get live fast, by all mean go with Heroku.

So what are the downsides? For me it's cost and flexibility.

### Cost

Heroku is free as long as you're running one "dyno" (somewhat equivelent to an AWS "compute unit," which we'll talk about later). As soon as you need more than one dyno, or a database, or memcached running somewhere, you start paying for it. Check out their [pricing calculator](http://heroku.com/pricing). 

It's tough to get a perfect one-to-one comparison to AWS equivelent offerings, but (as of August 2015) for a 1x dyno, Postgres database and 500MB Redis instance pricing _starts_ at $195/month. A similar setup[^awsservers] on AWS is free for the first _year_. A lot can happen in the first year of your app's life and when you're just starting you probably have much better ways to spend $200 a month. Oh, and after that first free year, those same servers would run you $35/month.

Now, Heroku is a full platform and includes support. With AWS you're running on (almost) bare metal—if something goes wrong it's up to you to fix it. You can pay for [dedicated support](http://aws.amazon.com/premiumsupport) from Amazon for $49/month, but if you set things up correctly and get a little experience with Linux you shouldn't need it. (Although even with their support cost, AWS still comes in at a lot less per month than Heroku.)

### Flexibility

Heroku is a Platform-as-a-Service and that's part of what you're paying for. They have years of experience tuning RoR applications for performance and ease of deployment and you benefit from that every time you deploy. However, when things don't work the way you expect, or you read about an awesome tweak you can make to nginx or Ruby's garbage collector you're out of luck. If you can't configure it with environment variables (or if Heroku decides it's a setting you won't be allowed to change) then tough luck.

With AWS you are the master of your own servers and you're free to tweak them to your heart's content. You have full root access to EC2 servers—install whatever packages you want, heck even patch the Linux kernel if you need. No one will stop you.

Of course with great power comes great responsbility: you could completely disable your entire server with a misplaced `rm -rf *`. You're protected from doing something like this on Heroku, but you're "protected" from any other common server tweaks or housekeeping you may want to perform as well.

## What you'll learn

We'll cover plenty of ground in this book, almost all of which may be new to you if you've never deployed a Rails (or any other) app to a production environment. We'll also cover installing some services that you may not have considered when developing locally but can quickly become essential to keep your app performing like a champ (like an in-memory cache and background job processor).

We'll cover:

* Amazon Web Service account setup
* Using the AWS Console to manage your services
* Preparing your Rails app for deployment
* Web server (nginx) setup and configuration
* App server (unicorn) setup and configuration
* In-memory cache (memcached) setup and configuration
* Background processing (Delayed Job) setup and configuration
* Third-party email service (Mandrill) setup and configuration
* Deploying and rolling back your code
* Performance and load testing
* Scaling strategies and high availability with AWS
* Continuous delivery/deployment
* Tools to monitor your app's performance (New Relic) and errors (Sentry) once live
* Upgrading services in the future

## What I assume you already know

I assume you're a software developer familiar with the web but don't assume you have production deployment experience. Some topics you should be familiar with:

* Basic git commands and usage—staging and committing changes, pushing and pulling to/from a repo
* GitHub repo management and access to the repo containing the code you're going to deploy (in reality you can deploy from anywhere, even a local codebase, but most people will probably be using GitHub)
* Ruby and Rails
* Basic Linux competency—changing directories, creating and editing text files, file permissions
* How the internet works—DNS basics, the request/response cycle, what HTTP headers are used for

## Thanks

I'd like to give a thanks to my friend Tom Preston-Werner for proofreading, editing and advice when writing this book. And thanks to you for (hopefully) paying for and reading it!

## Contacting Me

If you have feedback, found errors or have questions about topics you felt weren't thoroughly discussed in the book, get in touch at cannikinn@gmail.com. You can find me and lots of my code at GitHub as [cannkin](http://github.com/cannikin).

Also: write and let me know what you deployed! 

[^awsservers]: A t2.micro [EC2 server](http://aws.amazon.com/ec2/pricing), a t2.micro [RDS instance](http://aws.amazon.com/rds/pricing) and a t2.micro [Elasticache instance](http://aws.amazon.com/elasticache/pricing).
