---
title: Consolidating personal network services with Dokku and Docker
description: An overview of network infrastructure
date: "2014-04-15"
categories:
    - "blog"
    - "Heroku"
    - "Docker"
    - "Dokku"
    - "DigitalOcean"
    - "infrastructure"
    - "PaaS"
---

I run a number of network services for our household. These include [VPN servers][OpenVPN], handlers for web services like [Twilio][], and a couple of websites. Most of these aren't actually very interesting, but they need to work without a lot of fuss and without a huge investment of capital. File serving happens inside the house, but I found that I needed a number of independent services running outside in order to avoid network anarchy.

I ran a server on [Amazon EC2][] for a couple of years and was relatively happy with it; however, the cost and complexity led me to look for other options. EC2 is designed to operate at huge scale, and it has a lot of complexity for someone interested in one or two sites. I speak the language, and have a lot of respect for the service, but it wasn't a great fit for me.

Enter [Heroku][], a stable, managed platform designed to simplify deployment of web services and dynamic sites. Heroku is a PaaS (Platform as a Service) that was designed originally for Ruby apps, but which now supports a variety of languages and frameworks. Their way of doing things is attractive for a number of reasons:

- Each independent site/app is isolated in its own environment, with its own resources and dependencies.
- Deployment is set up in a structured and modular way, using [Git][]. Push your content to their site and it's live, with recursive dependency fetching.
- It's straightforward to add cloud-sourced backend services like databases, caching layers, analytics, etc. There's a whole ecosystem, and billing is centralized.
- There's a very capable free tier that lets you host small projects with no investment (and the same applies for most of the standard add-on services).

The free tier is great; you can spin up an arbitrary number of web apps, each of which runs in its own 512 MB sandboxed environment. The free server apps ("dynos", in Heroku's parlance) do spin down after a few minutes of disuse, and require a bit of time to fire back up when the next request hits. Hard to argue with free.

The problem with Heroku---if it can be called a problem---is that it's overkill for most personal projects. This makes a lot of sense if you're running a company off of it; however, their pricing is a harder proposition if the consequence of downtime is *mild inconvenience*, rather than *business apocalypse*. It's, like, $34.50/mo (USD) for the initial paid tier, as of March 2014. Speaking personally, that's well above my web-app [latte threshold][], though you definitely get rock-solid performance for the money.

On a personal note, I have limited discretionary funds before I have to start justifying such critical tasks as: backing up data three times, running carrier grade VoIP services for our house, insisting on UPS battery backups, making lighting computer controllable, running gigabit *everywhere*, etc.

## That'll do, Pig; that'll do

"There must be a better way!" you say. I think there is, and these things are developing quickly, so this may iterate a few times in the near future. Let's introduce things recursively:

[Dokku][] is a minimal open-source re-implementation of the Heroku [Buildpack][]-based PaaS infrastructure ("the smallest PaaS implementation you've ever seen"), which runs on
[Docker][], itself a set of services designed to make [LXC Containers][] usable. Docker is billed as "an open source project to pack, ship and run any application as a lightweight container". Docker is useful because it simplifies deployment of bundles that encapsulate the dependencies and functionality of a whole application stack.

Dokku makes it straightforward to depoloy things in a Heroku-esque environment running on your own hardware. It's a project by [Jeff Lindsay][], and you can find out a lot more on [his blog][DokkuBlog]. In the long run, it's looking like the successor, [Flynn][], will turn out to be even cooler and more capable (but it's still under development as I write this).

I run Dokku on [Digital Ocean][], which works fantastically-well for about $5/mo. See tutorials [here][HowToDokkuDO] and [here][DeployingDokkuDO]. Of course, it's quite trivial to install on your own hardware too; just check out the instructions in the [Dokku GitHub repository][Dokku]. Digital Ocean is also a great lightweight replacement for parts of [Amazon Web Services][], though certainly at a smaller scale.

For now, I'm going to avoid talking about setup in much detail. To create a new Dokku site, you just add a deployment target in the Git repository for your Buildpack/Procfile-enabled app:

````bash
git remote add dokku dokku@somekindasite.com:blog.git
````

When you push (`git push dokku master`), a whole bunch of cool things happen. First off, the push is handled by Git per normal. On the server end, the `dokku` user maintains a Git repository that receives the content. But, there's a receive hook (handled by [gitreceive][]) that checks to see if the target repository exists. If it *doesn't*, Dokku's underlying framework springs into motion and sets up the repository; it also starts putting together a Docker/LXC container to hold the app. Then, whether it was a new app or not, Dokku syncs in the new content and handles the buildpack---this means pulling any dependencies, setting up the app, and launching it as a service.

One really cool feature of Dokku: you can speecify the target domain in the Git remote address. In the example above, `blog` will coerce to a subdomain of whatever wildcard domain is set up for Dokku. See below for some examples (it's assumed that `somekindasite.com` is specified as the default domain for the Dokku installation). This works for arbitrary bare domains as well.

| Git Remote Entry                                    | VirtualHost            |
|-----------------------------------------------------|------------------------|
| `dokku@somekindasite.com:blog.git`                  | blog.somekindasite.com |
| `dokku@somekindasite.com:www.git`                   | www.somekindasite.com  |
| `dokku@somekindasite.com:www.somekindasite.com.git` | www.somekindasite.com  |
| `dokku@somekindasite.com:somekindasite.com.git`     | somekindasite.com      |
| `dokku@dramatichats.com.git`                        | dramatichats.com       |

Under the hood, this is making use of Docker containers with private networking. Dokku decides what ports to expose, and the connections for each app are reverse proxied using [Nginx][] Server Blocks (cf. VirtualHost entries) that are generated automatically.

So, you can run your entire workflow using Git, a couple of command-line scripts, and an easily-maintained [Ubuntu][] Server virtual machine. It may not work for everyone or everything, but it's simplified my workflows substantially and made it easier to maintain services and sites in my spare time.

Hit me back if this is useful to you!

[OpenVPN]: http://openvpn.net/
[Twilio]: https://www.twilio.com/
[Amazon EC2]: http://aws.amazon.com/ec2/
[Heroku]: https://www.heroku.com/
[Git]: http://git-scm.com/
[latte threshold]: /glossary
[Dokku]: https://github.com/progrium/dokku
[Buildpack]: https://devcenter.heroku.com/articles/buildpacks
[Docker]: https://www.docker.io/
[LXC Containers]: https://linuxcontainers.org/
[Jeff Lindsay]: https://twitter.com/progrium
[DokkuBlog]: http://progrium.com/blog/2013/06/19/dokku-the-smallest-paas-implementation-youve-ever-seen/
[Flynn]: https://flynn.io/
[Digital Ocean]: https://www.digitalocean.com/
[Amazon Web Services]: http://aws.amazon.com/
[HowToDokkuDO]: https://www.digitalocean.com/community/articles/how-to-use-the-digitalocean-dokku-application
[DeployingDokkuDO]: https://www.andrewmunsell.com/blog/dokku-tutorial-digital-ocean
[gitreceive]: https://github.com/progrium/gitreceive
[Nginx]: http://wiki.nginx.org/
[Ubuntu]: http://www.ubuntu.com/