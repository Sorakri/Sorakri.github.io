## Introduction

We are going to be working with Twitch's api version 5, and using it to search for channels.  API stands for Application Programming Interface, and API's are used to allow programs to interface with the service an API is made for.  In twitch's case, we can use their API to search their database for users, channels, or other information.  We are going to use this to find the channel named cohhcarnage, and find some videos from the channel, and embed them.

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
Next we open the request and put in the request method, which tells twitch which request method to use, and a url, which is where we are sending the request.  "Https://api.twitch.tv/kraken" is the base address, and we will be adding "/search/channels?query=cohhcarnage" which tells Twitch we want to search through channels for ones that have "cohhcarnage" as their name.  The last part is true or false, and tells if we are sending a synchronous or asynchoronous request, which determines if we wait for a response before continuing running our code or not.

```markdown
var req = new XMLHttpRequest();
req.open("get", "https://api.twitch.tv/kraken/search/channels?query=cohhcarnage", true);
```
Now for the header information to be included.  This is two thing:
```markdown
1. Our api Key, to identify the MNLHttpRequest is coming from an authorized source.  Generally api keys should not be shared, but for the purpose of this tutorial 
2. Since Twitch is in the process of switching from API version 3 to version 5, we want to specify we are using version 5
```
```markdown
var req = new XMLHttpRequest();
req.open("get", "https://api.twitch.tv/kraken/search/channels?query=cohhcarnage", true);
req.setRequestHeader("client-id", "iq3waqvzmhdf9xp01zj94ewrwjyqaa");
req.setRequestHeader("Accept", "application/vnd.twitchtv.v5+json");
```
Next we want to set up our function to receive the response from Twitch.  Twitch sends its responses as a JSON object, so we will want to parse it.  So that the rest of our program doesn't need to wait for a response to work, we had our request be ?synchronous; this means we need an event listener to check when we get a response, and then parse the response. It will also be helpful to add in a check for if the response takes to long to come, so we will add a check to the requests status that will log an error to the console if the response takes to much time.  Finally, we need to send the request at the end of everything.


```markdown
var req = new XMLHttpRequest();
req.open("get", "https://api.twitch.tv/kraken/search/channels?query=cohhcarnage", true);
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
We now have a program that will send a request to Twitch and recieve a response, what does that response look like?  Once parsed, it looks similar to this: 

```markdown
{
  "_total": 68,
  "channels": [
    {
      "mature": false,
      "status": "Horizon: Zero Dawn! - Roen & Laina not home quite yet! - Powered by Sony! - @CohhCarnage - !Achievements - !4Year",
      "broadcaster_language": "en",
      "display_name": "CohhCarnage",
      "game": "Horizon Zero Dawn",
      "language": "en",
      "_id": 26610234,
      "name": "cohhcarnage",
      "created_at": "2011-12-06T18:20:34.075423Z",
      "updated_at": "2017-03-03T03:32:18.489503Z",
      "partner": false,
      "logo": "https://static-cdn.jtvnw.net/jtv_user_pictures/cohhcarnage-profile_image-92dc409e41560047-300x300.png",
      "video_banner": "https://static-cdn.jtvnw.net/jtv_user_pictures/cohhcarnage-channel_offline_image-e7efe636e7920e39-1920x1080.png",
      "profile_banner": "https://static-cdn.jtvnw.net/jtv_user_pictures/cohhcarnage-profile_banner-bcb1b1b8e6194799-480.png",
      "profile_banner_background_color": "",
      "url": "https://www.twitch.tv/cohhcarnage",
      "views": 53216902,
      "followers": 737888
    },
    {
    9 more similar objects
    }
}
```

Since we asked Twitch to search all of its channels, we got a lot of results, shown by the "_total":68".  By default it gave us the first ten, but can give up to 100 objects in response. Other request types(Found at the Twitch [API dev page](https://dev.twitch.tv/docs)) such as a user request display different information.

## We have the information.  Now what?

The information we got was the names of 68 channels that had "cohhcarnage" in them.  The one we are interested in is the first one, who's name is only "cohhcarnage." The important thing is the ID, as the API request for a channels videos' requires the channel ID, not the channel name.  So now we will use our code again, changing the querry to search for videos of the channel with an ID of 26610234(the ID of cohhcarnage's channel).

```markdown
var req = new XMLHttpRequest();
req.open("get", "https://api.twitch.tv/kraken/channels/26610234/videos", true);
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

We get back some different information this time.  Like before, it shows the total results of the querry, in this case 3154 videos, and returns objects with information for the first ten videos.  Lets pick one of the videos, and embed it to play.

```markdown
{
  "_total": 3154,
  "videos": [
    {
      "title": "Horizon: Zero Dawn! - Roen & Laina not hope quite yet! - @CohhCarnage - !Achievements - !4Year",
      "description": null,
      "description_html": null,
      "broadcast_id": 24672908960,
      "broadcast_type": "archive",
      "status": "recorded",
      "tag_list": "",
      "views": 2181,
      "url": "https://www.twitch.tv/videos/125855214",
      "language": "en",
      "created_at": "2017-03-02T16:34:34Z",
      "viewable": "public",
      "viewable_at": null,
      "published_at": "2017-03-02T16:34:34Z",
      "_id": "v125855214",
      "recorded_at": "2017-03-02T16:34:17Z",
      "game": "Horizon Zero Dawn",
      "length": 20073,
      "preview": {
        "small": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb0-80x45.jpg",
        "medium": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb0-320x180.jpg",
        "large": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb0-640x360.jpg",
        "template": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb0-{width}x{height}.jpg"
      },
      "animated_preview_url": "https://vod-storyboards.twitch.tv/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/storyboards/125855214-strip-0.jpg",
      "thumbnails": {
        "small": [
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb0-80x45.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb1-80x45.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb2-80x45.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb3-80x45.jpg"
          }
        ],
        "medium": [
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb0-320x180.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb1-320x180.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb2-320x180.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb3-320x180.jpg"
          }
        ],
        "large": [
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb0-640x360.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb1-640x360.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb2-640x360.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb3-640x360.jpg"
          }
        ],
        "template": [
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb0-{width}x{height}.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb1-{width}x{height}.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb2-{width}x{height}.jpg"
          },
          {
            "type": "generated",
            "url": "https://static-cdn.jtvnw.net/v1/AUTH_system/vods_1dbc/cohhcarnage_24672908960_610727312/thumb/thumb3-{width}x{height}.jpg"
          }
        ]
      },
      "fps": {
        "audio_only": 0,
        "chunked": 60.00000000000008,
        "high": 30.021545765447605,
        "low": 30.021695215843774,
        "medium": 30.021545765447605,
        "mobile": 30.021695215843774
      },
      "resolutions": {
        "chunked": "1920x1080",
        "high": "1280x720",
        "low": "640x360",
        "medium": "852x480",
        "mobile": "400x226"
      },
      "channel": {
        "_id": "26610234",
        "name": "cohhcarnage",
        "display_name": "CohhCarnage"
      }
    },
    {
    9 more of these video objects, and 3144 that weren't sent.  That's a lot of videos for one channel
    }
```
We are not interested in most of the data, just the video ID, found near the top, and the resolutions, found near the bottom.  We want to embed this, so lets look back at the [Twitch API](https://dev.twitch.tv/docs/v5/guides/embed-video/) for how to embed a video. Here's the code.  


```markdown
<script src= "http://player.twitch.tv/js/embed/v1.js"></script>
<div id="SamplePlayerDivID2">
<script type="text/javascript">
	var options = {
		width: <width>,
		height: <height>,
		channel: <channel>,
	};
	var player = new Twitch.Player("SamplePlayerDivID2", options);
	player.setVolume(0.5);
</script>
```
