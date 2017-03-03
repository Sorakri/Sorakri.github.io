## Introduction

We are going to be working with Twitch's api version 5, and using it to search for channels.  API stands for Application Programming Interface, and API's are used to allow programs to interface with the service an API is made for.  In twitch's case, we can use their API to search their database for users, channels, or other information.

<!-- ### What that info looks like
 ![Image](response.png) -->

## Getting started

The first thing we need to do is to get ourselves an API key.  You do this by signing in or registering an account with twitch, then going to the connections tab in account settings. ![Image](settings-connection.png)

At the bottom of the connections tab, you will find a button to register your application
![Image](register.png)

This will bring up a page for you to register your application at.  It provides twitch with some information on the intent of your application, and is simple to fill out.  Once you have registered, their should be a field with an client-id in it.
![Image](cliend-id.png)

This id is what will identify your requests to twitch's services, letting it know your are authorized to ask and recieve information.  


## Now for some code

Now that we have an api-key, we can start with the code that actually calls the api.  There are multiple ways to do this, but we will use AJAX to make a XMLHttpRequest and send it to twitch.  To do this in javascript, we start with creating the base request.
```markdown
var req = new XMLHttpRequest();
```
Next we open the request and put in the request method, which tells twitch which request method to use, and a url, which is where we are sending the request.  "Https://api.twitch.tv/kraken" is the base address, and we will be adding "/channels/26610234" which tells it we want more information about the channel with the ID of 26610234.  The last part is true or false, and tells if we are sending a synchronous or asynchoronous request, which determines if we wait for a response before continuing running our code or not.

```markdown
var req = new XMLHttpRequest();
req.open("get", "https://api.twitch.tv/kraken/channels/26610234", true);
```
Now for the header information to be included.  This is two thing:
```markdown
1. Our api Key, to identify the MNLHttpRequest is coming from an authorized source.  Generally api keys should not be shared, but for the purpose of this tutorial 
2. Since Twitch is in the process of switching from API version 3 to version 5, we want to specify we are using version 5
```
```markdown
var req = new XMLHttpRequest();
req.open("get", "https://api.twitch.tv/kraken/channels/26610234", true);
req.setRequestHeader("client-id", "iq3waqvzmhdf9xp01zj94ewrwjyqaa");
req.setRequestHeader("Accept", "application/vnd.twitchtv.v5+json");
```
Next we want to set up our function to receive the response from Twitch.  Twitch sends its responses as a JSON object, so we will want to parse it.  So that the rest of our program doesn't need to wait for a response to work, we had our request be ?synchronous; this means we need an event listener to check when we get a response, and then parse the response. It will also be helpful to add in a check for if the response takes to long to come, so we will add a check to the requests status that will log an error to the console if the response takes to much time.  Finally, we need to send the request at the end of everything.


```markdown
var req = new XMLHttpRequest();
req.open("get", "https://api.twitch.tv/kraken/channels/26610234", true);
req.setRequestHeader("client-id", "iq3waqvzmhdf9xp01zj94ewrwjyqaa");
req.setRequestHeader("Accept", "application/vnd.twitchtv.v5+json");
req.addEventListener('load', function(){
	if(req.status >= 200 && req.status < 4000){
	}
	else {
		console.log("Error in req: " + req.statusText);
	}
	var response = JSON.parse(req.responseText);
	console.log(req.response);
 });
 req.send(null);

```
We now have a program that will send a request to Twitch and recieve a response, what does that response look like?  Once parsed, it looks like this: 

````markdown
{
  "mature": false,
  "status": "Horizon: Zero Dawn! - Roen & Laina not home quite yet! - Powered by Sony! - @CohhCarnage - !Achievements - !4Year",
  "broadcaster_language": "en",
  "display_name": "CohhCarnage",
  "game": "Horizon Zero Dawn",
  "language": "en",
  "name": "cohhcarnage",
  "created_at": "2011-12-06T18:20:34Z",
  "updated_at": "2017-03-03T01:04:48Z",
  "_id": "26610234",
  "logo": "https://static-cdn.jtvnw.net/jtv_user_pictures/cohhcarnage-profile_image-92dc409e41560047-300x300.png",
  "video_banner": "https://static-cdn.jtvnw.net/jtv_user_pictures/cohhcarnage-channel_offline_image-e7efe636e7920e39-1920x1080.png",
  "profile_banner": "https://static-cdn.jtvnw.net/jtv_user_pictures/cohhcarnage-profile_banner-bcb1b1b8e6194799-480.png",
  "profile_banner_background_color": null,
  "partner": true,
  "url": "https://www.twitch.tv/cohhcarnage",
  "views": 53216306,
  "followers": 737882
}
````

Since we only asked Twitch about one channel, we only got one object with information about a channel.  Other requests to Twitch may contain many more channels and their information in the response.
