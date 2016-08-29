---
title: Hack Stack
date: 2016-08-28 23:27:00
tags: 
- Docker
- Nginx
- Node.js
- MongoDB
- Hackathon
category: Development
---

I was recently part of a team taking part in the Royal Bank of Scotland API [hackathon](https://community.apigee.com/articles/27628/innovation-through-hackathons.html). Working flat out for a weekend to build something innovative was a fantastic experience, and I was lucky to be working with some really smart colleagues using an [API Management](https://apigee.com) platform that we were all familiar with. This weekend I will be setting up a full stack that I can use as the basis for any future projects or prototypes.
 
{% img /images/hackstack4 400 Hackathon %}

## The Stack

I will be creating a number of [docker](https://docker.com) images. These will allow me to select the components required for the project, and extend them as required. Additionally, I will use production-standard technologies so that I can iterate on a prototype if I would like to take a project further. Publishing the images to [Dockerhub](https://hub.docker.com) will also allow me to pull these images onto any local machine or budget server I can get my hands on for demonstrations. Here is how the simple stack will look:

{% img /images/hackstack1.png 300 HackStackComponent %}

## Frontend

In order to create lightweight and mobile-friendly frontend, I settled on hosting a simple [Bootstrap](http://getbootstrap.com/) template on [Nginx](https://nginx.com). For more complex front ends, I would consider the use of [Angular](https://angularjs.org/), [Ember](https://emberjs.com) or [React](https://facebook.github.io/react/), however these technologies can force unnecessary complexity on small projects. 

Please find below the [Dockerfile](https://docs.docker.com/engine/reference/builder/) created for the frontend, using the Nginx [base image](https://hub.docker.com/_/nginx/).

`Dockerfile`
```
FROM nginx

MAINTAINER Sean Davis

COPY /src /usr/share/nginx/html
COPY /nginx/nginx.conf /etc/nginx/nginx.conf 
```
[*Source*](https://github.com/seantdg/ProjectTemplate/blob/master/web/Dockerfile)

Typically, a web frontend would be communicating with public APIs. In order to reduce development complexity, I want to access the middleware APIs using [AJAX](http://api.jquery.com/category/ajax/) requests with relative paths. As such, I am using Nginx to host the static content AND proxy the middleware server. For a production setup, these would be separated.

`nginx.conf`
``` javascript
events {
	worker_connections 1024;
}

http {
	include /etc/nginx/mime.types;
	upstream myMiddleware {
		server middleware:3000;
	}
	server {
		location / {
			root /usr/share/nginx/html;
		}
		location /middleware {
			proxy_pass http://myMiddleware;
		}
	}
}
```
[*Source*](https://github.com/seantdg/ProjectTemplate/blob/master/web/nginx/nginx.con)

I have created a simple to-do list app to test connectivity of the complete stack. It also looks pretty good on mobile devices!

{% img /images/hackstack2.png 700 HackStackComponent %}

{% img /images/hackstack3.png 300 HackStackComponent %}


# Middleware

Any server side logic will be implemented in the middleware. I have chosen [Node.js](https://nodejs.org/) with [Express](expressjs.com) HTTP server as I find it much less verbose than the Java code that I wrote in my first development role. Please see the snippet below for an entire HTTP endpoint definition in less than 15 lines.

`app.js (snippet)`
``` javascript
app.get('/middleware/todos', function(req, res) {
	request.get({
		url:url + '/todo/entries',
	}, function(error, response, body){
		if(!error) {
			res.status(200).set({
				'Content-Type': 'application/json',
			}).send(body);
		}
		else {
			res.status(500).send({"success":false, "debug": + body});

		}
	});
});

```
[*Source*](https://github.com/seantdg/ProjectTemplate/blob/master/middleware/app.js)

Please find the Dockerfile I used for the middleware below. I could just have easily used a Node JS base image rather than an Ubuntu to remove the Node JS installation steps.

`Dockerfile`
```
# Set the base image to Ubuntu
FROM ubuntu

# File Author / Maintainer
MAINTAINER Sean Davis

# Install Node.js and other dependencies
RUN apt-get update && \
    apt-get -y install curl sudo && \
    curl -sL https://deb.nodesource.com/setup | sudo bash - && \
    apt-get -y install python build-essential nodejs npm && \
	ln -s /usr/bin/nodejs /usr/bin/node

# Install nodemon
RUN npm install -g nodemon

# Provides cached layer for node_modules
ADD package.json /tmp/package.json
RUN cd /tmp && npm install
RUN mkdir -p /src && cp -a /tmp/node_modules /src/

# Define working directory
WORKDIR /src
ADD . /src

# Expose port
#EXPOSE 3000

# Run app using nodemon
CMD ["nodemon", "/src/app.js"]

```
[*Source*](https://github.com/seantdg/ProjectTemplate/blob/master/middleware/Dockerfile)

## Database Interface

Prototypes that use static mock data are often used when demonstrating front end styling or user experience. In my opinion, dynamic prototypes are much more interesting, however the overhead of setting up database connectivity can take precious time away from hacking together something interesting. Apigee has a product call [Backend-as-a-Service](http://docs.apigee.com/api-baas), a RESTful interface on top of [Cassandra](https://cassandra.apache.org). By using this for development, we did not need database connectors, but could reuse our HTTP client in order to persist data.

As I want to control the hosting of the solution without buying a license, I wanted an alternative to BaaS. One option would be to use [CouchDB](http://couchdb.org/), which has a HTTP interface built in. I was unable to find many user stories for this however (aside from an interesting use case at the [Large Hadron Collider](http://readwrite.com/2010/08/26/lhc-couchdb/)). A quick search on NPM allowed me to find [mongodb-rest](https://www.npmjs.com/package/mongodb-rest), a Node.JS library that would provide a RESTful interface to the popular MongoDB persistence layer. When developing, I will not have to define any schemas or [models](http://mongoosejs.com), which will improve development time.

{% img /images/hackstack5.png 700 HackStackComponent %}

# Database
[MongoDB](https://www.mongodb.com) seemed a sensible choice and has a large [customer base](https://www.mongodb.com/who-uses-mongodb). It is a [NoSQL](https://en.wikipedia.org/wiki/NoSQL) database handles JSON documents gracefully. It is strongly consistent and supports partial tolerance at the cost of high availability. This is ideal for the prototyping. I would not use the for storing data for analytics purposes, and would instead using a Relational Database such as [Postgres](https://www.postgresql.org/). I also would not select this for very high traffic systems, and would instead use [Cassandra](https://cassandra.apache.org/) to improve availability.

In order to set up MongoDB, I simply used the [base image](https://hub.docker.com/_/mongo/) available on Dockerhub.


## Summary

I now have a template for all future web projects that is mobile compatible, and is using open-source popular software that can be scaled in future. Please find the code here:

Source code: https://github.com/seantdg/ProjectTemplate
Frontend Image: https://hub.docker.com/r/seantdg/frontendtemplate/
Middleware Image: https://hub.docker.com/r/seantdg/middlewaretemplate/
DB Interface Image: https://hub.docker.com/r/seantdg/dbfacade/
