---
title: "Creating a simple website using ChatGPT"
date: 2023-03-15
draft: false
tags: [ chatgpt, open-data ]
---

Software development has too many paradigms, programming languages and tools to know it all. This sometimes makes me a bit frustrated since I known a lot about some languages and tools but creating a simple site can seem too much effort. 

Now there's ChatGPT, without much effort or skill, here it's a site that shows Electric Grid Power Outages in Portugal https://vpereira01.com/electricalgridptqos/

I have wanted to create such site since I discovered that [E-REDES](https://www.e-redes.pt/en/about-us), the electrical power distributor, has an API that provides information about outages. Still, I always stopped when I thought the amount of stuff I would have to learn, debug and troubleshoot.

## Gathering Data

Since I also wanted to archive the data and not strain the API, and the license allowed me to, I decided to fetch the data using a GitHub Action that just CURLs the URLs.

I knew I could use BASH script and the JQ tool to handle the API pagination but I always trip up on the bash syntax and didn't want to learn JQ syntax. I asked ChatGPT and pretty much worked out-of-the [box](https://github.com/vpereira01/electricalgridptqos/blob/master/.github/workflows/fetch.sh).

## Building the Website

Upon prompted ChatGPT created the site using the Google Maps API. However, I discovered that the Google Maps API did not offer a free tier which was outside my project budget, zero euros.

After some research, I found that the Bing Maps API offered a free tier. I requested that ChatGPT make the necessary changes, and that was pretty much it.

It required some tuning and fixing but many times I just asked ChatGPT `Got the error "Uncaught TypeError: cluster.getPushpins is not a function", can you fix it?` and it was almost perfect.

## Summary

ChatGPT, and generally large language models, really leapfrog my capacity of creating simple stuff in languages and tools that I'm not proficient. Furthermore I think it will really help, in future iterations, anyone to create their own sites, applications, webservices as long they are curious and willing enough.