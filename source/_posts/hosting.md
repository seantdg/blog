---
title: Blog Hosting, Domains and Source Code
date: 2016-08-11 23:39:44
tags: 
- Node.js 
- Performance
- Containers
category: Development
---

Right now, this site is hosted on a $5 per month [Digital Ocean](https://www.digitalocean.com/) droplet. 

That gives me:
 - 512 MB Memory
 - 1 Core Processor
 - 20GB SSD

I am looking forward to running a quick performance test to see how much traffic it can handle. If the results are not satisfactory, I will look at how I can improve performance.

I have manually installed [Nginx](https://www.nginx.com/), [Git](https://git-scm.com/), [Node.js](https://nodejs.org/en/) and the [Hexo CLI](https://hexo.io/). It would be good to [containerize](http://www.docker.com/) this blog for a number of reasons.
 - My machine runs Mac OS X, Ubuntu and Windows 10. Maintaining a consistent environment for any project will be important.
 - Scaling will be easier to automate using [Kubernetes](http://kubernetes.io/) or [Docker Swarm](https://docs.docker.com/swarm/).
 
So watch this space for performance testing and containerizing!

