---
title: "Upgrading my simple site with Cursor.AI"
date: 2025-03-01
draft: false
tags: [electricalgridptqos, chatgpt, cursorai]
---

## The Challenge

I recently received an end-of-life notification for Bing Maps, with the recommended solution being to upgrade to Azure Maps. This affected my Electric Grid Power Outages site that I previously wrote about in another [post]({{< relref "chatgpt-pt-power-outage.md" >}}). Additionally, I wanted to explore using E-Redes OpenData API to directly obtain power outage data rather than relying on third-party services.

## Working with Cursor.AI

My experience using Cursor.AI for this upgrade was a mixed bag:

### The Good

- The "Agent" mode proved quite useful for integrating into my test loop
  - While somewhat slow, it was adequate for straightforward tasks
- Migrating to Azure Maps was handled reasonably well
- Refactoring to use E-Redes OpenData API was relatively straightforward

### The Bad

- The AI often generated poorly readable code
- Requesting refactoring sometimes resulted in losing previous requested changes
- The AI frequently forgot existing code rules, like properly handling empty data sets 

## Claude 3.7 with Cursor.AI

Using Claude 3.7 with Cursor.AI in Agent mode seemed like a significant improvement, but still required constant vigilance. The AI would frequently lose sight of the actual problem:

- When asked to fix a broken test, it would either:
  - Assume the test was broken and relax the test requirements
  - Assume the code was buggy and change it
- It lacked the understanding that when something fails, the first step is to determine what needs fixing—the code or the test

Despite these limitations, Cursor.AI was still a valuable assistant when working with technologies where I have limited proficiency.
