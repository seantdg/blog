---
title: Blog Hosting, Domains and Source Code
date: 2016-08-11 23:39:44
tags: 
- Node.js 
- Performance
- Containers
category: Development
---

Right now, this site is hosted on a $5 per month Digital Ocean droplet. That gives me:
 - 512 MB Memory
 - 1 Core Processor
 - 20GB SSD

I am looking forward to running a quick performance test to see how much traffic it can handle. Once I have gathered these results, I will configure a CDN and re-run the test to see the results.

I have manually installed Nginx, Git, Node.js and the Hexo CLI. It would be good to dockerize this blog for a number of reasons.
 - My machine runs Mac OS X, Ubuntu and Windows 10. Maintaining a consistent development environment for any project will be important.
 - Tracking which versions of each dependency I have successfully used is an additional overhead I don't need.
 - Scaling will be easier to automate using Kubernetes or Docker Swarm.
 
So watch this space for performance testing and docker-ising!

