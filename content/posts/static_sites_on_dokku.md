---
title: Static sites on Dokku
description: Dealing with some quirks of my favourite mini PaaS
date: "2014-04-17"
categories:
    - "blog"
    - "Dokku"
    - "infrastructure"
    - "PaaS"
---

I wrote about [Dokku][] in [a previous post][consolidating_network_services]; I spent a bit of time explaining why I think it's pretty much the greatest thing since sliced pizza. However, I ran into a minor issue recently when trying to set up a static site for this blog using [Hugo][]. Dokku doesn't always play nicely with static sites. I think this is improving (and it looks like [Flynn][] is much fancier); my issues occurred with Dokku *v0.2.1*.

In a world full of static site designers (e.g., [Jekyll][], [Hyde][]), the need for a PaaS (Platform as a Service) to run my blog is arguable. However, since I've been using [Docker][] and [Dokku][] wherever possible because of the simple deployment and update options, I wanted to keep everything within that ecosystem. Since [Hugo][] basically just spits out a directory full of HTML and static files, it's pretty straightforward to serve. There are some built-in options that use [buildpacks][Buildpack] to deploy [Nginx][] or [Apache][]. I tried both, and ran into the same issue with both; for now I'll focus on the default Nginx buildpack.

## Setup 

If you put a file in your git repository's root directory named `.nginx`, the default [Nginx buildpack][] fires up and handles serving a site from `/app/www`. This means that the document root is the `www` directory of your git repository. Cool. I set up [Hugo][]'s `config.yaml` file as follows:

```yaml
---
contentdir: "content"
layoutdir: "layouts"
publishdir: "www"
indexes:
category: "categories"
baseurl: "http://ostensibly.me"
title: "ostensibly.me"
---
```

**NB:** The whole site is stored in a [public repository on GitHub][GitHub/ostensibly] for your reading pleasure.

This worked great. I loaded up [ostensibly.me][] and was pleased to find that everything was *awesome*. Until I clicked a link.

It turns out that, for reasons that are a bit complex, I got redirected to the wrong page. Instead of `http://ostensibly.me/info/about/`, I ended up at `http://ostensibly.me:5000/info/about/`. There's no server running on port 5000; however, the Nginx server does use port 5000 at one point in the indirection when Nginx proxies the connections for Dokku.  So, I removed the `:5000` bit from the link in my address bar, hit `Enter`, and the next page loaded. Clicked a link. Port 5000, **404**'d! [It bears pointing out that it wasn't actually a `404` error, since the site just didn't respond at all. But it's still a good idiom.]

## What happened?

The links on [ostensibly.me][] point to things like `http://ostensibly.me/info/about`, and when the pages load up the `/` gets tacked on the end by Nginx. But, Nginx was rewriting the URLs to include port `5000` when it did the rewrite. Since the requests were being rewritten, all the links were affected. However, going directly to `http://ostensibly.me/info/about/` was fine, since no rewrite happened.

[The Solution][] came from *Hariadi* on [StackExchange][], who suggested modifying the Nginx config to add a `proxy_redirect` entry.

```
proxy_redirect http://ostensibly.me:5000/ http://ostensibly.me/;
```

This change works fine, and solves an annoying problem. My impression from reading pull requests on GitHub is that [Flynn][] is going to solve all of the world's problems, this among them. Until then, my site works.

[Apache]: http://projects.apache.org/projects/http_server.html
[Buildpack]: https://devcenter.heroku.com/articles/buildpacks
[Docker]: https://www.docker.io/
[Dokku]: https://github.com/progrium/dokku
[Flynn]: https://flynn.io/
[GitHub/ostensibly]: https://github.com/bsmithyman/ostensibly.me
[Hugo]: http://hugo.spf13.com/
[Hyde]: http://ringce.com/hyde
[Jekyll]: http://jekyllrb.com/
[Nginx]: http://wiki.nginx.org/
[Nginx buildpack]: https://github.com/rhy-jot/buildpack-nginx
[ostensibly.me]: http://ostensibly.me/
[StackExchange]: http://stackexchange.com/
[The Solution]: http://serverfault.com/a/408024

[consolidating_network_services]: /posts/consolidating_network_services/