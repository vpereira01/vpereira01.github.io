---
title: "Why is Zoom help page broken? A router IPv6 bug"
date: 2022-05-21T10:48:54+01:00
draft: false
tags: [ troubleshoot, ipv6 ]
---

Trying to enable Zoom blur background in Linux I end up several times on the [Zoom Virtual Background system requirements](https://support.zoom.us/hc/en-us/articles/360043484511) page which was always broken, as in, clicking on the Linux section did not expand to show the relevant information. It was annoying but I could reach the relevant information using the Firefox Web Developer Tools.
Nonetheless, it was bothering that it was always broken and if it was broken for everybody Zoom people would already handled it.


## First troubleshoots and hypothesis

Checking Firefox Web Developer Tools console it was very likely the issue was caused by a failure to get JQuery library as JQuery is used a lot for these kind of interactions, i.e. click to expand. As it is a good practice, the first steps I took was to do simple variations to quickly eliminate hypotheses and hopefully find some commonality to the issue. 

In this case, the first things I wanted to vary were:

* The browser, since some sites don't play well with Firefox and/or the extensions I use;
* The DNS resolver, since some sites/CDNs/services expect people to use the default ISP DNS resolver to work properly/optimally;
* IPv6 enabled/disabled, because I have already seen some issues like slower speeds with IPv6;
* The operating system, for the sake of eliminating the possibility that my Linux distribution is a factor.

After testing these variations, at my home network, surprisingly the issue was related to IPv6.

**Hypothesis 01** The server/CDN serving JQuery has some issues handling IPv6 requests.

The JQuery library was being served from https://code.jquery.com/ and searching around I quickly found a few GitHub issues related to IPv6 so this hypothesis seemed solid. The last GitHub issue [codeorigin.jquery.com/82](https://github.com/jquery/codeorigin.jquery.com/issues/82) was still open so I just added information that I also had a similar issue.

Unfortunately, the feedback from the issue indicated there was nothing wrong with serving JQuery specifically.

## Second hypothesis, something is broken in my ISP

Discussing the issue on GitHub and doing further tests new information was obtained:

* Not all requests with `curl` and IPv6 failed, around 1 in 7 were successful;
* Testing other URLs hosted on the same CDN provider showed the same behaviour.

This had me suggest that something might be wrong with CDN provider handling of TLS connections, given the `curl` logs information, because I know CDNs  usually have some kind of custom TLS connection termination. Still it lead me to a new hypothesis:

**Hypothesis 02** Something is wrong with my ISP IPv6 handling.

With this hypothesis and knowing that the next step in the troubleshoot would require sending a packet capture to the CDN support, which I didn't had a way to do it privately and/or knew a way to share publicly without exposing private information, I decide to investigate this hypothesis.

I went to some local tech communities' chats and asked around. Initially I got some kind of confirmation as a guy remembered having similar IPv6 issues with Python PIP packages and ISP support could not figure out the issue. Still this hypothesis was quickly destroyed as other people, with the same ISP and IPv6, could make the requests with no issues.

I had the time, and was still trying to avoid sending a packet capture, so I started searching for IPv6 issues and somehow hit the jackpot, I found the GitHub issue [pip/5374](https://github.com/pypa/pip/issues/5374) that mentioned Python PIP packages, IPv6, my ISP and my router :)

## Third hypothesis, something is broken with my router

According to the pip issue my router has a bug which changes, during TCP connection, the IPv6 Flow Label which breaks some load balancing algorithms and thus the connection is dropped. The information was useful but the solutions found required disabling IPv6 or getting a new router which were not that satisfying and I really wanted to fully confirm this hypothesis.

###### What the hell is IPv6 Flow Label?

As far as I know, because I'm not a network expert, IPv4 is a more pure datagram protocol since an IPv4 packet header does not contain information about if a packet belongs to any TCP/UDP connection/stream. Furthermore a router inspecting IPv4 packet headers coming from the same source and to the same destination can not easily infer if the source/destination are maintaining zero or many TCP connections. IPv6 seems to deviate from this as it contains the IPv6 Flow Label header field in each "flow" of packets (TCP connection/UDP stream) is labeled. Even ICMP pings seem to be labeled. 

The rules for this labeling seem to have evolved over time but the current standard as I understand it is:

* When a network socket is open, the OS, attributes a Flow Label which is used on all packets sent;
* The Flow Label can be determined by (source, destination) or (source, destination, protocol, source port, destination port).

The goal for IPv6 Flow Labels seems to reduce the computation costs of load balancing by reducing the need to inspect transmission protocols headers (TCP,UDP,etc.) as IPv6 headers suffice.

If you want to dig around further I suggest starting with this:
* https://github.com/marian-babik/ipv6_flow_label
* https://blog.apnic.net/2018/01/11/ipv6-flow-label-misuse-hashing/

###### Testing hypothesis

Knowing all this I lurked around kernel parameters to see if there was an easy way to disable IPv6 Flow Labels, as in, always set them to zero. I expected that my router would not assign a Flow Label if there was none present.

Searching around I found that on [ip-sysctl.txt](https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt) which contains the promising parameter `auto_flowlabels` which could pretty much disable all IPv6 Flow Labels.

Now this was seem an easy test to make :)

{{< gist vpereira01 71caee5d418a4106a6e5bccd6dfd9de5 >}}

###### Fixing the issue

After the issue was confirmed I decide to not immediately ask my ISP for a router fix/replacement. Some CDN providers have improved their compatibility with non-standard use of IPv6 and I would need this broken router to confirm a fix. So I reported my finds while also understanding if they would not try to change their infrastructure for a router behaviour that probably affects very few people.

Not only the issue was fixed quickly as they expressed gratitude in the information provided :)

## Afterthoughts

First I would like express gratitude to
* JQuery folks for the support and having a public place to discuss issues;
* Highwinds/StackPath folks for fixing the issue and being so nice;
* Pip folks for doing the initial investigation and finding the root cause of the issue;
* Joel from Fastly for publicly writing about the IPv6 Flow Label. 

If someone wants to dig further, a question that was left hanging was why didn't Firefox and Chrome fallback to IPv4 worked. Both seem to implement the "Happy Eyeballs" algorithm, which is at a glance, should have kicked in and hidden the IPv6 issue.

Anyway, now I can restart trying/whishing to have blurred backgrounds in Zoom with Linux.