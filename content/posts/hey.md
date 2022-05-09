---
title: "Hi and site setup"
date: 2022-05-07T15:59:34+01:00
draft: false
tags: [vpereira01]
---

# Hi and welcome to my site :)

For a while I thought it was a good idea to set up a personal site, or blog, but like others I had always postponed it.
Recently because I lived through an interesting tech story I finally decided to do it.

Since this is my first site/blog please excuse my lacking writing skills.

## Choosing a domain and registrar

My GitHub username has ended up being my handle/nick on most professional and tech platforms so reusing it seemed like a good option.
Having the "01" on "vpereira01" does not sound great but it's preferable to have a recognizable name than to try to find a better domain.

Although I have used other registrars in the past, this time choosing Cloudflare was an easy choice to make. 
It seems to have the lowest, and more stable prices, for .com domains. I have considered using a .dev domain but it was not supported by Cloudflare and they are generally more expensive.

An extra reason to use Cloudflare is that it's more future proof. I can easily protect it behind Cloudflare proxies and/or host it elsewhere than GitHub Pages.

## Why GitHub pages?

Out of the gate the idea was to use GitHub Pages because it's free, simple and I'm already using GitHub.
Another important factor is the added value of extending a personal site with GitHub social features, as in, people who find this site can also check out my work and interactions on GitHub.

From the simplicity aspect of GitHub Page I highlight two aspects. 
First, I can just write markdown documents and they serve as static HTML pages. 
Second, it's easy to serve these HTML pages from a custom domain with HTTPS.

## Static site generator

By default GitHub Pages uses Jekyll to transform markdown documents into static HTML pages. 

Even if for this site I'm trying to focus more on writing markdown than to tinker with tech its nice to have the possibility. Also I wanted to be able quickly render my posts, and site tweaks, locally so it would be faster to iterate on them.
Unfortunately Jekyll is implemented in Ruby, which I know close to nothing, and could be a problem. Still, since Jekyll it's the default tool I decided to give it a go.

##### Jekyll local dev/test issues

Trying to follow Jekyll Quickstart [page](https://jekyllrb.com/docs/) I just installed Ruby Ubuntu package but then trying to install Jekyll it required "sudo" permissions to "Building native extensions" which was concerning.
The Jekyll installation takes a while because of the "Building native extensions" step which it's annoying but not a deal breaker.

When writing this post I noticed that there are better instructions for Ubuntu on a different page which avoids the "root" permissions, [link](https://jekyllrb.com/docs/installation/ubuntu/). Still, following them does not work because there's a bug with Jekyll [link](https://github.com/jekyll/jekyll/issues/9031).

The Jekyll speed didn't impress me much as I'm now used to Go tools which are incredibly fast.

In sum, Jekyll doesn't seem that friendly for those who are not familiar with Ruby and there are probably other/better options out there.

##### Hugo

I searched around for static site generators and immediately noticed Hugo because it was written in Go. I decided to try and immediately was happy with its speed and how easy it was to bootstrap. Yes, I'm familiar with Go which helps a lot.

From nothing to a test site in a few seconds (if you already have a Go installation):

  1. `go install github.com/gohugoio/hugo@latest`
  1. `hugo new site quickstart`
  1. `cd quickstart`
  1. `hugo new posts/hey.md`
  1. `hugo server -D`


## Setting up Continuous delivery

The advantage of GitHub Pages is to commit a markdown and auto-magically your site is updated. Since I'm not using the default Jekyll this now requires more configuration from my part.

   1. Serve site content from `gh-pages` branch instead of the default branch
   1. Setup a GitHub Actions workflow that updates the `gh-pages` branch automatically

This was easy enough since there are GitHub actions to do all the work, I just had to follow this [instructions](https://github.com/peaceiris/actions-gh-pages#getting-started).

## Email routing

When you have a personal site it's always a nice touch to provide a way for random visitors to reach out to you.

Cloudflare provides free email routing for registered domains which it's great. I just needed to use that feature and forward emails to my private email box.

## To Do

Things that I still want to improve on this site and its infrastructure

* Send emails from this domain.
* Generate, and store, site statistics without 3rd party tracking.
* Support/Enable RSS support.
* Improve UI.

## Final note

I will try to keep the [about](/about) page updated with all the tech used to keep this site running.
