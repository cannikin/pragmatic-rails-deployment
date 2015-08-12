# Introduction

Hello, world! The purpose of this book is to get your Ruby on Rails web app up and running in a free, fast, scalable environment. Specifically Amazon Web Services and its awesome suite of virtual servers, databases, file storage and other cloud infrastructure bits.

## Why I wrote this book

I think Infrastructure-as-a-Service, and AWS in particular, is one of the most profound changes in technology since the internet itself. Suddenly everyone has access to world-class hardware for literally pennies per hour. But like any new technology it's still somewhat like the wild, wild west: everyone has their own way to do things and conflicting information abounds.

There's also the if-you-don't-use-this-new-tool-you'll-regret-it advice and how-the-big-guys-do-it blog posts. People get caught up in the hype when Facebook discusses their platform and how they deploy—"if you're not using X deploy tool then your app isn't **web scale**!" But honestly, is your current project going to need to scale to Facebook levels in the near future? Probably not. Save yourself days and weeks of struggling to learn the latest and greatest deploy tools that were written by and for people with much different problems to solve than you have (or may ever have).

What I'm presenting in this book is a no-nonsense approach to getting your web app online with a minimum of ceremony and complexity. Does [Chef](http://chef.io) have it's place? Absolutely. But Chef's learning curve is like walking up to the base of El Capitan. Unless you're deploying and maintaining dozens or hundreds of servers (like Facebook) I don't think that learning curve is worth it just to get your brand new app (with zero users) available to the world. As a former collegue was fond of saying: the juice isn't worth the squeeze. (Don't feel bad if you don't know what Chef is. We'll be discussing that and many more common, but at times unnecessary, deployment tools.)

That being said, your app should be prepared for reasonable growth and disaster recover:

* One of your app servers suddenly dies
* A junior developer accidentally runs `DELETE FROM users;` against production
* Your launch makes it to the front page of Hacker News
* An image on your homepage suddenly becomes the [next great meme](http://cdn.meme.am/instances/500x/17349069.jpg)

Your site must remain available and serving users with a minimum of downtime (ideally none). The setup and deployment I discuss will keep you up in all of these scenarios, and more. 

You may start to think up increasingly unlikely disaster scenarios (the [transatlantic communcations cables](http://en.wikipedia.org/wiki/Transatlantic_communications_cable) are severed by a rogue submarine at the same time a zombie outbreak hits the east coast of the United States!) and try to figure out how to keep your site up. This is possible within the AWS infrastructure, although I won't have the room in this book to discuss the ramifications of the undead taking over multiple Amazon data centers. Sorry.

## Why you should trust me

I've been a professional web developer and designer since 1998. 

I've been deploying RoR applications in production since 2006 and have lived through the evolution of dedicated to virtual servers, FastCGI to Passenger to Mongrel to Thin to Unicorn, self to cloud hosting.

I've been in charge of maintaining apps with one user (myself) to apps serving millions.

I'm a full-time contract Web Developer (Software Engineer, Computer Programmer, pick your title) and a smattering of Dev Ops usually comes with the territory. I've found that when you start with a solid infrastructure setup then the dev ops part of the job takes care of itself for the most part.

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

* Amazon Web Service account management and setup
* Preparing your Rails app for deployment
* Scaling strategies
* High availability with AWS

## What I assume you already know

* Basic git commands and usage
* Ruby
* Rails
* Basic Linux competency (changing directories, creating files, file ownership)
* How the internet works (what DNS does, the request/response cycle, what HTTP headers are used for)

## Thanks

I'd like to give a thanks to my friend Tom Preston-Werner for proofreading, editing and advice when writing this book. And thanks to you for (hopefully) paying for and reading it!

## Contacting Me

If you have feedback, found errors or have questions about topics you felt weren't thoroughly discussed in the book, get in touch at cannikinn@gmail.com. You can find me at GitHub as [cannkin](http://github.com/cannikin).

Also: write and let me know what you deployed! 

[^awsservers]: A t2.micro [EC2 server](http://aws.amazon.com/ec2/pricing), a t2.micro [RDS instance](http://aws.amazon.com/rds/pricing) and a t2.micro [Elasticache instance](http://aws.amazon.com/elasticache/pricing).
