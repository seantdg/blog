---
title: A Quick Blog Load Test
date: 2016-08-21 00:00:00
tags: 
- Nginx
- Performance
category: Development
---

After setting up this blog on the cheapest [server]() I could I find, next I want to identify exactly how much traffic it can handle. Currently it is only serving static content from [Nginx](), so I would expect it to be pretty fast. The aim of this experiment is to confirm that the blog can handle a reasonable amount of traffic. If usage ever reaches 20tps+ (bots and humans) then I will be very happy!

In my experience, [JMeter]() is a popular choice by testers. It is an intuitive, extensible load testing tool written in Java. Whilst the Swing GUI feels very [clunky]()(lmgtfy) and outdated, the JMX scripts generated are [widely supported]() by cloud testing prodivers.


An alternative open source tool that can be run from the command line is [curl-loader](). I had been recommended this by a colleague as a very high performance load testing tool written in C using HTTPS, FTP and TLS/SSL client protocol stacks. Venturing into the [README]() is not for the faint-hearted.

I timeboxed my experiments with both tools, and pointed them to an [Apigee]() API Proxy that just returned 200 OK. The [analytics]() dashboards are available on free accounts, so I found this to be an easy way to identify the amount of traffic that my local machine could generate.

Curl Loader:
{% img /images/perf1.png 700 CurlLoaderResults %}

JMeter:
{% img /images/perf3.png 700 JmeterResults %}

As you can see, with no tuning whatsoever, JMeter peaked at 456 tps vs. 210tps for Curl Loader. As such, I stuck with JMeter wrote a test against this blog.

After ramping up over a number of tests, I was able to generate 300tps from my local machine, with an average response time of 251 ms. As the error rate was 0%, I was keen to use a cloud provider to see if I could push it a bit further.

{% img /images/perf5.png 700 TPS %}
{% img /images/perf6.png 700 Aggregate %}

After signing up for a account with [Loader](), I was able to run 3 tests for free. This allowed me to use ~10,000 virtual clients over a 60 second window. At this traffic level, a 5.4% error rate was observed as a result of network timeouts and errors. For now, I am pleased with these results!

{% img /images/perf4.png 700 Loader %}

This was a fun test to understand the limits of this blog, however for a production system this would not be sufficient. I have not identified the point at which performance degrades, nor have I observed the spike vs soak behaviour. It would also be impossible to generate a significant amount of traffic from my local machine, however by using [serverless]() computing technology such as [Amazon Lambda]() a large amount of traffic could be generated on demand.

