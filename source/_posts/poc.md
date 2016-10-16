---
title: Building a Proof of Concept
date: 2016-10-16 23:00:00
tags: 
- Docker
- Nginx
- Node.js
- MongoDB
- POC
category: Development
---

Using the [Hack Stack](/28/08/2016/hackstack/), I have spent a few hours building a Proof of Concept. I wanted to demonstrate how easy it was to extend the Project Template to build a working prototype that may for example be the result of a Hackathon or a weekend coding with friends.
We hear about [Digital Disruption](http://apigee.com/about/blog/digital-business/digital-disruption-i-love-apis-europe-2016) every day, whether banks are [shutting down](http://www.standardmedia.co.ke/business/article/2000219368/equity-bank-boss-sees-death-of-branches-as-customers-go-digital) their branches or Uber is driving London Cabbies [off the streets](https://techcrunch.com/2014/01/19/uber-and-disruption/). Personally, my career has been built on [disrupting traditional](https://community.apigee.com/accelerator-methodology/blueprint) development methodologies and rapidly building innovative solutions. Businesses that give the new kids a chance to prove them wrong, will often find themselves ahead of the curve. Us new kids just need to prove our ideas to them quickly! 

Here I will use the simple example of a Fantasy Formula 1 App. The UI is a simple extension of the [Bootstrap](http://getbootstrap.com/) web page. The dynamic elements are populated using [AJAX](https://github.com/seantdg/ff1-poc/blob/master/web/src/js/myJs.js#L9) requests to the [Node JS](https://github.com/seantdg/ff1-poc/blob/master/middleware/app.js) middleware.

{% img /images/pocui.jpg 600 POC %}

The middleware leverages [ExpressJS](http://expressjs.com/), and mediates data between two targets. This project leverages [Ergast API](http://ergast.com/mrd/), an Open Formula 1 REST API that contains statistics from long before I was born. The second is the [RESTful interface](https://github.com/seantdg/ff1-poc/tree/master/dbHttpInterface) we have deployed in front of MongoDB.


`middleware/app.js`
``` javascript
function calculatePredictionResults(predictions, results) {
	for(var i = 0; i < predictions.length; i++)  {
		var prediction = predictions[i];
		var predictedPosition = prediction.position;
		try {
			var actualPosition = results[0][prediction.circuit][prediction.driver];
			prediction.result = (predictedPosition == actualPosition ? "correct" : "incorrect");
		}
		catch(e) {
			prediction.result = "pending";
		}
		predictions[i] = prediction;	
	}
	
	return predictions;
}
```

The application will allow you to make predictions on Formula 1 races, and will tell you if your predictions are correct, incorrect or pending. As it is a POC, there is no identity provider, leaderboard or prediction validation. However it has allowed me to show that you can create an application to demonstrate an idea in a matter of hours with a few hundred lines of code.

We can now look to productionise this application.


Source Code can be found [here](https://github.com/seantdg/ff1-poc).

