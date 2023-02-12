---
layout: blog-post.njk
bodyClass: "blog-post"

seo:
  title: A new PeerTube instance for urbanists
  description: "A new PeerTube instance for urbanists"
  canonicalOverwrite: ""

blogTitle: "A new PeerTube instance for urbanists 🚶🚲🚊"
date: "2023-02-11T12:00:00Z"
author: ""
image: "/assets/images/blog-images/first-toot.png"
featuredBlogpost: true
featuredBlogpostOrder: 1
excerpt: |-
  The fediverse is truly amazing. A toot that I thought would get a few likes, got an incredible amount of engagement from around the world.
---

The fediverse is truly amazing. A toot that I thought would get a few likes, got an incredible amount of engagement from around the world. I really doubt that this would have happened on Twitter.

I hope that you will find urbanists.video a useful tool to share and learn from videos to build more walkable, livable places around the world.

## Status page

There is now a status page for urbanists.video located at https://status.urbanists.video. If you are curious if you're just having internet connectivity issues, or if urbanists.video is having problems, this is a great place to start.

This is one of the many different ways I'd like to keep urbanists.video as transparent as possible. It would be interesting to eventually spin up a Grafana dashboard.

## DigitalOcean

When I first started up urbanists.video, I was trying to keep it contained to home server hardware. This was intended to reduce costs and make sure that urbanists.video remains viable in the long term.

However, I had no idea that urbanists.video would get such a warm welcome by so many on Mastodon. In particular, [@seabikeblog@social.ridetrans.it](https://social.ridetrans.it/@seabikeblog) was one of the first to try out video uploading with [this really awesome format for a video](https://www.seattlebikeblog.com/2023/02/10/watch-bike-blogger-tries-to-fly-a-plane-above-seattle-again/) to discuss the joy (and a little pain) of cycling in Seattle! Unfortunately, the home internet connection I have, combined with a couple layers of reverse proxying and virtual hosts just wasn't cutting it. It took a few attempts for Tom to get the 1.5GB video file uploaded with a lot of timeout issues. And worse, I couldn't replicate it.

So I decided to move urbanists.video to DigitalOcean. I really want to ensure that people have a good uploading experience, because I don't want anyone to get frustrated and give up on posting a video. [I decided to go by the book](https://joinpeertube.org/en_US/faq#should-i-have-a-big-server-to-run-peertube), and urbanists.video now resides on a $21/mo droplet with 2 GiB RAM, 2 ("premium AMD") vCPUs, 3 TiB transfer, and 60 GiB storage. I hope this droplet will treat us well for quite some time.

> PeerTube should run happily on a virtual machine with 2 threads/vCPUs, [...] You will hugely benefit from at least a second thread though, because of transcoding.

Videos are still moved to object storage on backblaze b2 after transcoding. That's been working very well, and even streaming 4k works great! Because b2 has free egress when proxied through a partner (such as Backblaze), transfer is free (just pay $0.005/gb/mo storage!).

Nightly backups of videos, misc assets, and postgres are all configured and running smoothly (3-2-1).

## It's always DNS

In the process of migrating, I forgot that I left the DDNS service on for the domain. So late at night, the DDNS service decided to point the urbanists.video domain back to my home server, causing 5 hours and 36 minutes of downtime. This really reinforces the benefit of multiple admins in different timezones.

On the upside, it was a very easy fix.

## Devops with PeerTub

Over the past week I've learned a _ton_ about how PeerTube works. It's been a pleasure to use, with just a couple snags.

1.  Mentioning a user really doesn't work well. The link is often broken, which is a problem that's really been magnified now that so many people are using Mastodon and wanting to link from their profiles. Luckily, this is being tracked already in Github: https://github.com/Chocobozzz/PeerTube/issues/5557
2.  Backblaze b2 doesn't support object-level ACLs, which causes PeerTube to fail to upload private videos to object storage. Not great, especially due to the limited storage on the VM. The good news: this is also being worked on! https://github.com/Chocobozzz/PeerTube/issues/5497
3.  I really don't know enough about PostgreSQL. On my home server the database was hosted in Docker. So I used `pg_dump` and tried to import it into the new DigitalOcean VM (not using docker) with `pg_restore`. When I tried to run PeerTube, it kept crashing with obscure error messsages. After many hours of research, I finally discovered the `pg_restore` flag [`--no-owner`](https://stackoverflow.com/a/31470664/1319878). That one flag was all I needed, and the rest of the migration was flawless.

## Spam

We... already got spam comments. 😢 It seems to be coming from PeerTube instances with open registration. I may have to end up muting those servers if there's no better option. Let me know via Discord or Mastodon if you have any ideas.

## Blog

As you can tell, urbanists.video now has a blog! Not much to it, just part of my effort to keep operations as transparent as possible. [The blog itself is open source, too!](https://github.com/urbanists-video/blog)
