
## Introduction

We are going to be working with NASA's API to learn how to search for images from the Spirit, Opportunity and Curiosity rovers on mars.  An API is an "Application Programming Interface," and are used to allow programs to interfacte with a service for users.  With NASA's API, you can do many things, but we are going to use it to search for a picture from the first day Curiosity landed on mars.



# Getting an API key

The first thing we need is to get an API key.  This is what identifies your requests to the API service, to let it know you are autorized to communicate with the service.  This is done by going to https://api.nasa.gov/index.html#apply-for-an-api-key and entering your information; the key you get back should look like a string of random numbers and letters.  It is important for security to not share these keys.

# Now to make requests

With an API key, we can now start making the code that sends requests to the API.  There are several ways to do this, but we will use AJAX to make an XMLHttpRequest and send it to NASA's API service.  To do this in javascript, we start with by first creating the request with:
```markdown
var req = new XMLHttpRequest();
```

next, we open the request and tell it what method to use, in our case it is "get", and the address url of the API service we are sending the request to.  "https://api.nasa.gov/mars-photos/api/v1" is the base address of the mars photos api, and we will add "/rovers/curiosity/photos" to tell it we want photos from the rover Curiosity.  Next, we add ?sol=0" to tell NASA that we only want images from the first solar day that Curiosity was on mars.  If we wanted to search something else, we would look at "https://api.nasa.gov/api.html#MarsPhotos" to see more query parameters.  Finally, at the end of the request address, we add "&api_key=YOUR KEY HERE".  Normally, we don't want our webpages to wait for a response to load everything else, so at the end of our request, we set asynchronous to be true.  This means that the second line of our code ends up as:
```markdown
req.open("get", "https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?sol=0&api_key=3z5eaPz4uM9hTQVUmHUgA0HSYeRrmBWv2SvGBVbE", true);
```

Now that we have our request made, we want to set up our code to get NASA's response. Since this is an asychronous request, we need to have an event listener that activates when it receives a response from NASA.  We will also add in an error message if there is an error with the response. Once we get the response, we want to log it to the console to see the response.  At the end of our code, we finally send the request. To do this we use the code:
```markdown
	var req = new XMLHttpRequest();
		req.open("get", "https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?sol=0&api_key=3z5eaPz4uM9hTQVUmHUgA0HSYeRrmBWv2SvGBVbE", true);
		req.addEventListener('load', function(){
			if(req.status >= 200 && req.status < 400){
			}
			else {
				console.log("Error in req: " + req.statusText);
			}
			console.log(req.responseText);
	});
	req.send(null);
```

Now that we have some code that can send a request to NASA and recieve results, what does the result look like? To look at it we need to look at the console, which is where we told our code to send the requests' response. What we see is an object which holds several thousand photo objects that look similar to:

```markdown
{"id":58889,"sol":0,"camera":{"id":23,"name":"CHEMCAM","rover_id":5,"full_name":"Chemistry and Camera Complex"},"img_src":"http://mars.jpl.nasa.gov/msl-raw-images/proj/msl/redops/ods/surface/sol/00000/opgs/edr/ccam/CR0_397506434EDR_F0010008CCAM00000M_.JPG","earth_date":"2012-08-06","rover":{"id":5,"name":"Curiosity","landing_date":"2012-08-06","launch_date":"2011-11-26","status":"active","max_sol":1708,"max_date":"2017-05-26","total_photos":315354,"cameras":[{"name":"FHAZ","full_name":"Front Hazard Avoidance Camera"},{"name":"NAVCAM","full_name":"Navigation Camera"},{"name":"MAST","full_name":"Mast Camera"},{"name":"CHEMCAM","full_name":"Chemistry and Camera Complex"},{"name":"MAHLI","full_name":"Mars Hand Lens Imager"},{"name":"MARDI","full_name":"Mars Descent Imager"},{"name":"RHAZ","full_name":"Rear Hazard Avoidance Camera"}]}}
```
Cleaning it up a bit, it looks like 
```markdown
{
      "id": 9726,
      "sol": 0,
      "camera": {
        "id": 20,
        "name": "FHAZ",
        "rover_id": 5,
        "full_name": "Front Hazard Avoidance Camera"
      },
      "img_src": "http://mars.jpl.nasa.gov/msl-raw-images/proj/msl/redops/ods/surface/sol/00000/opgs/edr/fcam/FLA_397502305EDR_D0010000AUT_04096M_.JPG",
      "earth_date": "2012-08-06",
      "rover": {
        "id": 5,
        "name": "Curiosity",
        "landing_date": "2012-08-06",
        "launch_date": "2011-11-26",
        "status": "active",
        "max_sol": 1708,
        "max_date": "2017-05-26",
        "total_photos": 315354,
        "cameras": [
          {
            "name": "FHAZ",
            "full_name": "Front Hazard Avoidance Camera"
          },
          {
            "name": "NAVCAM",
            "full_name": "Navigation Camera"
          },
          {
            "name": "MAST",
            "full_name": "Mast Camera"
          },
          {
            "name": "CHEMCAM",
            "full_name": "Chemistry and Camera Complex"
          },
          {
            "name": "MAHLI",
            "full_name": "Mars Hand Lens Imager"
          },
          {
            "name": "MARDI",
            "full_name": "Mars Descent Imager"
          },
          {
            "name": "RHAZ",
            "full_name": "Rear Hazard Avoidance Camera"
          }
        ]
      }
    },
```


Several thousand of these in a result is quite a lot.  Lets see if we can make it easier to deal with.  Looking at https://api.nasa.gov/api.html#MarsPhotos, we see we can add "&page=1" to only receive 25 results at a time.  Lets do this, and change our address to now looks like:
```markdown
https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?sol=0&page=1&api_key=3z5eaPz4uM9hTQVUmHUgA0HSYeRrmBWv2SvGBVbE
```

25 photo objects at a time is a lot easier to look through than several thousand, and is a lot easier to load.  While each photo object holds a lot of information, we are only interested in the image itself, which is found in the img_src part of the photo object, and is a url to that image. Lets look at the image from the first result of our search
![first image from curiosity](http://mars.jpl.nasa.gov/msl-raw-images/proj/msl/redops/ods/surface/sol/00000/opgs/edr/ccam/CR0_397506434EDR_F0010008CCAM00000M_.JPG)
It isn't very interesting, just a white circle.  So lets see if we can find a better image.  Once again, we go back to "https://api.nasa.gov/api.html#MarsPhotos" and see what we can do.  It looks like searching by camera could help us, and looking at the list of cameras, the Panoramic camera on Opportunity or Spirit sounds interesting.  Lets change our request to look for images from Spirit's Panoramic camera on its 100th day on mars.  To do this, we change our request address to be 
```markdown
https://api.nasa.gov/mars-photos/api/v1/rovers/spirit/photos?sol=100&camera=pancam&page=1&api_key=3z5eaPz4uM9hTQVUmHUgA0HSYeRrmBWv2SvGBVbE"
```
Sending our new request, we get an object holding information on 25 images from spirits panoramic camera.  Lets look at the last image in the object this time.

![pancam image from Spirit](https://mars.nasa.gov/mer/gallery/all/2/p/100/2P135241955ESF2702P2111R1M1-BR.JPG).
This is much more interesting then that white dot from earlier.

# Thats all.
Well, now you know how to use the Mars Rover API; sending it requests, looking through the results, and using the API's page to learn how to structure the requests.  Our final code was 

```markdown
var req = new XMLHttpRequest();
		req.open("get", "https://api.nasa.gov/mars-photos/api/v1/rovers/spirit/photos?sol=100&camera=pancam&page=2&api_key=3z5eaPz4uM9hTQVUmHUgA0HSYeRrmBWv2SvGBVbE", true);
		req.addEventListener('load', function(){
			if(req.status >= 200 && req.status < 400){
			}
			else {
				console.log("Error in req: " + req.statusText);
			}
			console.log(req.responseText);
		});
		req.send(null);
```
