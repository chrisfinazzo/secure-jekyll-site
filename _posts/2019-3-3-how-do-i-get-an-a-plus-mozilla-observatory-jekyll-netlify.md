---
layout: post
title:  "How do I get an A+ in Mozilla Observatory with Jekyll and Netlify?"
tags: [blog, tutorial, jekyll, netlify, observatory, security, JAMstack]
description: "Learn how to get an A+ grade through Mozilla Observatory using Jekyll and Netlify."
---

When I need to build a blog or static site, my go-to is JAMstack with [Jekyll](https://jekyllrb.com/) and [Netlify](https://www.netlify.com/).

I also try and build out secure websites. I used to think it really only mattered on sites that process personal or sensitive information. But then I listened to [episode 250 of the Shop Talk Show](https://shoptalkshow.com/episodes/250-web-security-april-king-alex-sexton/). April King and Alex Sexton talked about the different ways even static sites can get hijacked, serve up malicious content, and otherwise make the internet a bad place. 

After listening to that podcast, I started using the [Mozilla Observatory](https://observatory.mozilla.org/) to audit all my projects and improve my security. In most cases, fixing security vulnerabilities was straightforward, but I have consistently had issues configuring the proper security headers with Jekyll and Netlify.

Here's how I finally got an A+ for my Jekyll/Netlify site on Observatory. 

## Step 1: Create a new Jekyll project 

In a terminal, run `jekyll new secure-jekyll-site`. I've named this project `secure-jekyll-site`, you can choose any project name you wish.

Change into the secure Jekyll directory by running `cd secure-jekyll-site`. 

Initialize the git repository with `git init`.

Make the initial git commit with: 

```
git add .
git commit -m "init commit"
```

[Upload the repo to GitHub](https://help.github.com/en/articles/adding-an-existing-project-to-github-using-the-command-line). 

## Step 2: Deploy to Netlify 

Follow the instructions to [deploy a Jekyll site to Netlify](https://www.netlify.com/docs/continuous-deployment/).

Netlify's tools are beyond the scope of this post, but they are excellent. If you're into JAMstack development, I'd recommend getting familiar with them.

For now, let's continue by securing our initial deploy. The goal is to start off with the most restrictive set of policies possible. What you should find as you build out your site on top of this boilerplate is certain assets (images, styles, scripts) won't load as expected. That's the goal. You'll have to learn to modify your headers and security policies to allow these assets and other functionality to your site. This practice will put you in a position to make intentional choices about which policies to relax. 

## Step 3: Check the initial installation with Observatory 

Visit the [Mozilla Observatory](https://observatory.mozilla.org/) and enter your Netlify domain in the form. You'll likely find the scan comes back with a grade of **D+**. 

In the top right hand corner, in the box labeled **Recommendation**, you'll find Mozilla provides easy-to-read and helpful advice to improve your security headers. This blog post won't cover those in depth. Mozilla will prompt you to make one change at a time and re-scan your site. If you want to get a good feel for the process, I'd recommend going step by step. 

The trick I found was figuring out how to deploy these changes to the live site. For the life of me, I couldn't figure out what I was missing. The rest of this blog should clarify the process of updating and improving your headers for the Mozilla Observatory security check. 

## Step 4: Add a headers file and include it in your Jekyll build process

In order to provide Netlify with custom headers, you need to add a `_headers` file to your project, [as outlined in the Netlify docs](https://www.netlify.com/docs/headers-and-basic-auth/). Add this file in the **root of your Jekyll project**. 

However, if you were to run `jekyll build` or deploy Netlify right now - the `_headers` file wouldn't be available. Jekyll isn't set up to add it to the compiled `_site` folder yet. 

To [configure Jekyll](https://jekyllrb.com/docs/configuration/options/) to add the `_headers` file to your build process, use the `include` option. Inside your `_config.yml` file, add `include: ["_headers"]` to _config.yml. 

Now when you build your Jekyll site, `_headers` will appear in the site root on Netlify. you can add the appropriate fixes as instructed by Mozilla. 

## Step 5: Add restrictive headers to your site 

Now that `_headers` works on your Netlify deploy, you can add the appropriate headers to get an **A+**. Again, I'd recommend you make these changes step by step, following along with Mozilla to understand each piece of the process and make your own informed decisions. These are the headers that provide an **A+** on the boilerplate Jekyll project:

```
/* 
    Content-Security-Policy: base-uri 'self'; default-src 'none'; form-action 'self'; frame-ancestors 'self'; img-src 'self'; script-src 'self'; style-src 'self'
    Referrer-Policy: same-origin
    X-Content-Type-Options: nosniff
    X-Frame-Options: DENY
    X-XSS-Protection: 1; mode=block
```

## Conclusion 

With these headers, a blank Jekyll site should score an **A+** on Mozilla Observatory with a score of **120/100**. It will also clear all the Content Security Policy checks they run. 

It will be as restrictive as possible, so you may find you need to make adjustments to these headers to get certain tools and components to work on your site. Again, it's good practice to make the necessary changes intentionally, vs. trying to fix broken security policies retroactively. 

## Next steps 

As I mentioned, some components may not work properly with these restrictive settings. The first you'll notice is the icon set provided by the [minima theme](https://github.com/jekyll/minima). In the footer, you'll notice there ought to be a GitHub and Twitter icon. Those won't load with these settings, but changing `default-src` in the content security policy to `'self'` should do so. Consider [reading about default-src at the MDN docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) before making this change, yourself. I won't be making it, to keep this boilerplate project as restrictive as possible. 

If you'd like to use this boilerplate for your own Jekyll projects, you can [fork the Secure Jekyll Site repo](https://github.com/ogdenstudios/secure-jekyll-site). You can also see the live site [here](https://secure-jekyll-site.netlify.com/).

*All the Observatory scores listed here are current as of March 3, 2019*