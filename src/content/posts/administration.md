---
layout: blog-post.njk
bodyClass: "blog-post"

seo:
  title: Intro guide for urbanists.video admins
  description: "Intro guide for urbanists.video admins"
  canonicalOverwrite: ""

blogTitle: "Intro guide for urbanists.video admins"
date: "2023-03-12T12:00:00Z"
author: ""
image: "/assets/images/blog-images/do.png"
featuredBlogpost: true
featuredBlogpostOrder: 1
excerpt: |-
  A lot of the knowledge on instance administrations currently isn't documented, simply because the instance was just getting started, so that's why this blog post exists! And in order to remain as transparent as possible, all of this information is publicly available through this blog.
---

I'm really excited to have [@mattcaff@union.place](https://union.place/@mattcaff), also known as Heartland Urbanist, helping out with administrating urbanists.video. 🎉 One of the goals for this PeerTube instance is to have it cooperatively owned and operated by its creators. There are no walled gardens here, no secrets, and no "leader." Welcome, Matt!

If you are interested in helping out, get involved! If you have the time, join our [Matrix chat](https://matrix.to/#/#urbanists.video:matrix.org), create a channel and upload videos! Or if you don't have the time but still want to support the work we do, donate to our [OpenCollective](https://opencollective.com/uv).

With that being said, a lot of the knowledge on instance administrations currently isn't documented, and there's a lot of stuff that only I (Alex) have been involved in, simply because the instance was just getting started. So I'm creating this blog post in order to impart some of the instance administration details. And in order to remain as transparent as possible, all of this information is available publicly through this blog!

To get started, I'm going to try to explain all the various services and moving parts that make urbanists.video possible from a high level.

I'll try to keep this document up-to-date. Feel free to help out and keep this document up to date as well!

## So you want to be an admin

Keep in mind that as an admin, you have access to sensitive data such as user emails, logs, etc so be sure to secure any device with access.

- Use a password manager, don't reuse passwords, use 2fa auth where possible
- Keep your computer and software up-to-date

## All about our paid services

These are all of the paid services in which we need to have a payment method with. Some of them, like Backblaze and AWS, will be $0 due to low use.

Paid services obviously cost money, but are generally more stable long term and more likely to respect privacy.

Any service that's critical to u.v's operation will be marked with a 🚨. Backups are considered critical.

### 🚨 Digital Ocean — Our server

Digital Ocean (https://www.digitalocean.com) provides the server that runs u.v (known as a Droplet). We are currently running on a single `s-2vcpu-2gb-amd` Droplet hosted in the NYC1 datacenter.

We use a Digital Ocean "team" where various people can be added, so we don't need to share account credentials. Two factor authentication is required for every person with access.

Server access via SSH can be provided by reaching out to someone with access (currently, just Alex) with your public key. As a last resort or in an emergency, SSH access can be provided by using the Digital Ocean recovery console. SSH access should only ever be needed for updating PeerTube and regular server maintenance.

The server is set up with PeerTube recommendations and best practices (https://docs.joinpeertube.org/install/any-os). A further blog post will go into this in more detail.

💰 $21/month

### 🚨 Cloudflare — r2 block storage for videos, DNS

We currently use Cloudflare's paid r2 block storage (https://www.cloudflare.com/products/r2) for video hosting. Because video files are large and relatively bandwidth intensive, this reduces the load and requirements of our Digital Ocean server. And because Cloudflare r2 has free egress (video file downloads), we don't have an unexpected bill if u.v gets a lot of traffic.

How it works: When a video is uploaded to u.v, it's optimized for web streaming and then uploaded to Cloudflare r2. Once published, video viewers will directly request bandiwdth-intensive video files directly from Cloudflare r2, bypassing our server entirely.

Similar to Digital Ocean, we have set up a team where individuals can be added in order to avoid sharing account credentials. You can access r2 by going to the Cloudflare team dashboard and clicking "R2" in the sidebar. From there you can see objects (u.v video files), metrics, and various bucket settings.

There are a few access tokens. One is for the u.v server and has read/write access. Another, with read access, allows Alex's home server to back up all video files nightly. This backup is then replicated to two other servers Alex controls, one offsite, for redundancy. Access to these servers is secure and encrypted.

We use Cloudflare r2 instead of Backblaze, because it is against Cloudflare Terms of Use to use their proxy for free egress _unless_ using r2. Cloudflare r2 is the cheapest block storage available while fully complying with Terms of Use regarding video files.

Cloudflare r2 costs about $15/tb/mo, so it's essentially free at the time of this writing as we only have ~5gb stored (0.004tb).

We also use Cloudflare for domain nameserver hosting and some other small things, and that's free.

💰 ~$0 (right now)

### 🚨 Porkbun — Domain names

We use [Porkbun](https://porkbun.com/) to hold our domains, https://urbanists.video and https://urbanist.video. It costs about $20 per domain, per year.

💰 ~$40/year

### 🚨 Amazon Web Services — Transactional emails

We use Amazon Web Services' Simple Email Service (AWS SES https://aws.amazon.com/ses) to send transactional emails from PeerTube (for example, forgot password, create account, notifications etc). Our volume is significantly below the free limit of AWS SES, so this service doesn't cost anything.

The reason we use AWS SES is its a very reputable service with high rates of deliverability to ensure important transactional emails reach users' inboxes.

💰 ~$0

### 🚨 Backblaze

Backblaze (https://backblaze.com) provides object storage for automated nightly backups of the Postgres database and non-video peertube files (config, thumbnails). All data is also replicated from this bucket to Alex's personal server for further redundancy.

At some point, another blog post will be dedicated to u.v's backup strategy (and restore procedure).

💰 ~$0

### 🚨 Proton Mail — ✉️ Email

We use Proton Mail (https://proton.me) to manage our shared inbox, hello@urbanists.video. We also sometimes share files with Proton Drive.

💰 $5/month

## All about our free services

### 🚨 Bitwarden

Shared credentials are saved in a Bitwarden organization. Generally, we try to avoid using shared credentials and use a team where possible (for example, Digital Ocean and Cloudflare). Never send a password to anyone via other means—always refer to the Bitwarden vault.

### 🚨 OpenCollective

OpenCollective is how we raise funds and pay expenses for the above services in a transparent way. https://opencollective.com/uv

### Grafana

We use Grafana (https://grafana.urbanists.video) for insights on how the server is doing. For example, CPU, memory, RAM, number of requests per time interval, etc.

We also have [a public dashboard](https://grafana.urbanists.video/public-dashboards/05ae33d9f6ac444cb1d1781681670a22?orgId=1&refresh=15s) so that anyone can view server stats. This allows us to be transparent, especially when there are issues u.v.

Grafana is self-hosted by Alex in a docker container on a home server, so we don't need to pay for it. It's not a critical service. Basic server metrics can also be found through the Digital Ocean dashboard.

### Matrix

We have a Matrix chat room hosted for free by matrix.org. It's public, so anyone can join and see what we're up to. https://matrix.to/#/#urbanists.video:matrix.org

### Mastodon

Our Mastodon account is used for general communication and showcasing videos: [@video@urbanists.social](https://urbanists.social/@video).

### Hetrix Tools

We use Hetrix Tools for our status page (https://status.urbanists.video). This can ping us via hello@urbanists.video when the server goes down, has TLS issues, long response times etc.

### GitHub

We have a GitHub org (https://github.com/urbanists-video) that has public repositories for our blog (that also hosts the blog via Github Pages) and any other misc code. That's what you're reading right now! Reach out via Matrix to be added to the Github org.

## FAQ

### The server is under high load. What to do?

If the server is under high load (you can confirm by checking https://grafana.urbanists.video), turn on Cloudflare proxying for the server by changing Proxy Status—DNS Only to Proxy Status—Proxied. Generally, we should have proxying **off** (DNS Only) in most situations (proxying can cause issues with video uploads).

If the load is sustained, we can also upgrade the Droplet temporarily or permanently, but that will result in up to 15 minutes of downtime.

### ... your question?

Feel free to expand on this FAQ section. If you have any questions, let's try to capture them here for people in the future!
