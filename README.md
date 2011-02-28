BigDoor
=======
BigDoor supports gamification at the API level with RESTful web service.

Points, virtual currency, awards, levels, and badges are stored, granted, and retrieved using HTTP requests. More information about the service is available for developers at publisher.bigdoor.com.

bigdoorjs
=========
bigdoorjs is a javascript library that simplifies use of the web service. Rather than exposing the structure of the web API, it implements a subset of the functionality that is consistent and easy to implement.

bigdoorjs can be used by node.js and in-browser applications.

conventions
-----------
bigdoorjs is object oriented javascript but it does not use the "new" keyword. Objects are returned by functions not by constructors. This is still under consideration so the way it is may change. If you have an argument either way, send it my way.

Examples
--------
The simplest form of gamification is giving points to users as a reward for an action. Experience points in RPGs, chips in poker, your score in Pacman, your post count on a forum--these are all examples of points given to users. Points can also be called virtual currency or just currency--they are all the same in bigdoorjs.

Points are the backbone of a game. bigdoorjs makes awarding and tracking these ppoints easy.

The very first step is to create the currency that you will be tracking. Maybe this is XP, or Mana, or goals, or even VirtualBucks.

For example, lets take XP.

Before we can do anything we must get a publisher object. A publisher can be thought of the as the very basic connection to the BigDoor webservice.

node.js:
	var app_key = 'd6e92052c79b4f329c1f79c3a87ce604';
	var secret_key = 'b0435cee6f3d413c97754574cddeb3f8';
	var publisher = require('bigdoor').publisher(app_key, secret_key);

Then, once the publisher object is created, it is used to created a new currency object:
	var xp = publisher.currency({
		title: 'XP',
		description: 'Experience Points awarded to the user for performing valuable actions'
	});

This object represents a new currency. However, it can not be given to the user until it is created in the BigDoor API. Remember--the BigDoor API is used to strore and retrieve data. Objects created locally must be written to the server to be used. This is done using the save method.
	xp.save(function(error, callback) { });

This pattern should be familiar to node.js users--since saving the object requires a request to another server it uses a callback to communicate the result. All resource objects (meaning any object retrieved or created through the publisher interface, like currency for example) take a callback function when accessing the server. This is done when saving or retrieving items. And all callbacks must have the following signature:
function(error, data) 

If error is null or undefined then the request was a sucess and data holds the response body. Otherwise, data is null or undefined and error contains the error information. error and data are mutually exlusive.

Once the Experience Points currency has been saved, it can be given to a user using the give-to pattern (which is also used for awards and goods).

To give 50 Experience Points to user example_user we call give and to on xp.
	xp.give(50).to(example_user, function(error, new_currency_balance) { });

This will give 50 Experience points to example_user and returns her new balance (or total number of points for that currency) through the callback.
