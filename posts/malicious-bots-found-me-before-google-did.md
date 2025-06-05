---
title: "Malicious bots found me before Google did"
author: "Piotr Niewęgłowski"
date: "2025-06-01"
description: "Even small sites get scanned by bots. Learn how to protect your project with real examples, security tips, and a production-ready checklist."
slug: "malicious-bots-found-me-before-google-did"
tags: ["security", "hackers", "bots", "build-in-public"]
---
## Can unpopular sites still get hacked?

I have. And to be honest, I thought that this kind of malicious activity was extremely unlikely in my case.

I implemented a simple analytics module for PushBlog. Nothing too sophisticated - just counting views grouped by UTM tags. I shipped it to production, and checked the results in a few hours. When I ran a `SELECT *` query in DB I was shocked. I saw a large number of suspicious requests such as 
```shell
  "/cgi-bin/phpinfo.php",
  "/db.ini",
  "/db_backup.sql"
```

When I dug deeper into it, it was even more interesting!
```shell
    "/.aws/config",
    "/.env",
    "/web.config",
    "/.git/config",
    "/.git/branches",
    "/.git/head",
    "/.git/index",
    "/.git/logs",
    "/.git/logs/head"
```

And even something quite fresh like `"/openapi.json"`. I remember that once I bought a domain and provisioned the cloud infrastructure, I noticed in the Cloudflare dashboard that some requests had been made to my page. That was surprising for me, and I had no idea how on Earth somebody could find my page if I hadn't shared that with the world. I hadn’t even published a sitemap file. After seeing those requests I realized what was going on. Bots were trying to steal secrets and take control of my erver or even more interestingly over my cloud account. My server could have been turned into a crypto mining rig while I was sleeping if the setup had been insecure. 

Here’s a screenshot from my Cloudflare logs showing some of those suspicious requests:
![Cloudflare logs](/assets/malicious-bots-found-me-before-google-did/cloudflare.png)

## I checked if this was common

I found many questions on Stack Overflow. The latest I found were asked in 2012: [Suspicious requests in Apache web server log file](https://stackoverflow.com/questions/12454448/suspicious-requests-in-apache-web-server-log-file)
And in 2015: [Server log file HEAD requests](https://stackoverflow.com/questions/30689682/server-log-file-head-requests). That was a confirmation for me - such requests are normal, just another day in the office. 

I kept digging into the topic. As you can imagine, malicious actors aren’t idiots. If they do the action regularly, they need to have a clear aim. They need to succeed from time to time to justify the effort. I found questions that showed engineers make configuration mistakes from time to time. And that is the low hanging fruit for such bad players. Here are the questions I found:
[.env file exposed only when accessing via https and website IP address](https://stackoverflow.com/questions/70687266/env-file-exposed-only-when-accessing-via-https-and-website-ip-address), and
[Nuxt 3 expose .env file to all](https://stackoverflow.com/questions/75474601/nuxt-3-expose-env-file-to-all).

## If others make these mistakes, I could too

We're all humans. Humans make mistakes. Period. I asked myself a question if I did everything as it should be, to protect me in case I made a mistake. The setup should ensure that even if I’m having a bad day and not fully focused, nothing bad can happen in this area. I identified areas which need to be secure from a day one.

### Use .gitignore as your first defense

You need to have .gitignore, and you should have listed all files containing secrets. This may sound trivial but a few years ago, [Uber had a really severe security incident](https://www.wired.com/story/uber-paid-off-hackers-to-hide-a-57-million-user-data-breach/) because they committed a file with credentials to a private (!!!) github repository. If that happened to such great engineers like them, it's very likely that you or I could run `git add .` and miss an important file in the list of changes. 

### Use Docker carefully

Similarly to .gitignore you need to have proper setup related to .dockerignore file. No .env or config file should go there. Apart from that, you should avoid copying your entire source code into the Docker image. Just copy the bare minimum needed for the deployment. The last thing is - you should use non-root user:

```shell
...
# Add a non-root user 
RUN adduser -D appuser
...
# Switch to non-root user
USER appuser
...
CMD ["your_command"]
```

### Secure your production config

To be more secure, you should use environmental variables instead of .env files when building and running the application outside your local environment. 

These days frameworks and web servers are very decent. It’s hard for me to imagine a server exposing its entire file system by default. You need to configure routing in order to be able to serve something. However, it might be one exception from this. A `static` folder with your assets. A lot of servers can allow you to serve the whole content of such a folder. Be careful and keep the content of your assets as lean as possible.

### Don’t expose your internal routes

It happens sometimes that technical files of your infrastructure are created in an automated fashion. I think particularly about two must-have files for every client facing web page: sitemap.xml and robots.txt. If you include every render-able page in your sitemap, that’s a bad idea. The file should help web crawlers to index pages, all you want to insert here are public pages. Revealing internal or admin pages is a bad idea. 

When it comes to robots.txt, it's a bit more complicated. You want to tell crawlers not to enter your private territory. Assuming you didn't include them to sitemap, and there is no direct link from any other pages - you should be fine. It's very unlikely that crawlers will find these routes. However, you may think it's not enough, you want to block them explicitly - and that's good. At first let's look on how you should not do that:

```shell
User-agent: *
Disallow: /admin
```

If you add such a definition to robots file - everyone knows that you have /admin route. That's bad, again. So here's what you should do to make sure that it's not visible for everyone, and tell crawlers, that they should go away.
For backend side, use a custom HTTP header:

```shell
X-Robots-Tag: noindex, nofollow
```

For rendered HTML, use meta:
```html
<meta name="robots" content="noindex, nofollow">
```

Now you're all set. Two things good to know: 
- The main crawlers like Google respect those hints, but you need to remember that they are just hints and some other actor can ignore it.
- Your crawling budget will be used for such pages. Not great, not terrible as it was said in one of the great TV series. The most important thing is you didn't reveal your structure to everyone.

### Bonus: block bots with your CDN

In my case I used Cloudflare workers. I defined exact routes and patterns frequently asked by malicious scanners, and I'm returning **HTTP 404**. As a result, they are blocked, My server doesn't receive any of those requests, and they don't know if I have such a resource they asked. I could return **HTTP 403**, but I would leak my internal structure - I don’t want to give them any clues. Let’s keep it a mystery.

### Set up alerts and budget limits

Last point, but very important. You need to have proper alerting and budget in place. If you assume to spend X money for the whole month, and you’ve burned 85% of it in just the first three days of the month? Alert! That should raise a flag. Maybe not red, but definitely orange. Someone is trying to hack you or you misconfigured something. Both of those occasions should be an alert. There is also a third option: your popularity exploded and surprised you - in such case, change your settings and congrats from me, you deserved. 

A budget as some 'circuit breaker' can save you too. Let's imagine that your page is under attack, and you're on holidays not checking an email. Or just sleeping. There could be any number of reasons. That’s just life. Life is unpredictable and you need digital insurance in the form of proper budget configuration.

## Can I proactively improve my security?

As it's easy to anticipate the answer - yes, you can. There are plenty of tools which can help you. Please take a look for those two:

- [Nikto web server scanner](https://github.com/sullo/nikto)
- [Trivy, a comprehensive and versatile security scanner](https://github.com/aquasecurity/trivy)

They are massively popular (check github stars), and well documented. It's always better to check for potential issues before releasing the product.

## A production-ready security checklist

Here’s a condensed version you can use as a checklist. Below, there is a production-ready security checklist. Feel free to copy, and check all of those actions before going production. 
The list may not be complete for your case. If it is so, use it as a starter. The topic is too important to not pay attention. 


```shell
[ ] `.gitignore` and `.dockerignore` include secrets
[ ] No `.env` files in deployment artifacts
[ ] Use non-root Docker user
[ ] Use ENV vars in prod
[ ] Review static/public folder content
[ ] Validate `robots.txt` and `sitemap.xml`
[ ] Block known bot patterns via proxy/CDN
[ ] Run tools like `nikto` and Docker scanners
[ ] Set cloud provider budget alerts
```

## Final thoughts

At the very beginning, I thought that these malicious bots can be described as something interesting to know. A cool story to tell to my friends. However, Stack Overflow questions taught me that the threat is real. In majority cases you're safe by default if your engineering workflow meets decent standards. We need to remember that bad actors hunt for this very small niche, misconfigured servers. If they find from time to time prizes like **AWS credentials**, they are rewarded. That's pure gold for them.

If that happened to me, and I had to tell my wife that this summer we need to skip holiday because I need to pay a high cloud-infrastructure bill - I'm almost certain, she would not be happy. Even if you’re developing a small side project, even if it’s just used by you and your friends, you still need to make sure you're not revealing too much.

If you liked the post, share that and save you friend from becoming a best friend of malicious crypto-miner!