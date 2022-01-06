---
title: Phase 1 Project
---

<h2>Coffee Story</h2>

The main learning goals of this assignment was to design and architect features across a frontend by using Javascript and an external API. This was really exciting for me because it would be the first time I used a public API to build a project. The requirements for this project were as follows: 

- Your app must be a HTML/CSS/JS frontend that accesses data from a public API. All interactions between the client and the API should be handled asynchronously and use JSON as the communication format.

- Your entire app must run on a single page. There should be NO redirects. In other words, your project will contain a single HTML file.

- Your app needs to incorporate at least 3 separate event listeners (DOMContentLoaded, click, change, submit, etc).

- Some interactivity is required. This could be as simple as adding a "like" button or adding comments. These interactions do not need to persist after reloading the page.

- Follow good coding practices. Keep your code DRY (Do not repeat yourself) by utilizing functions to abstract repetitive code.

I spent the first two days just learning how to use and manipulate the google maps API. I didn't really know what I wanted to create but I knew I wanted to do something with my recent love for coffee! I initially was thinking of making a personal directory of my favorite coffee beans with a link to order more whenever I needed. I quickly realized that I wouldn't be able to create one the way I wanted while being constrained to just one html file. After bouncing ideas around I finally landed on developing an informational site that helps answer all the questions I had when I first got into brewing coffee.


<h2>Google Maps API</h2>

Lets start with the google maps API I used. In order to access google's wealth of data you first have to create a billable account. I didn't understand this at first but as I went through the process of working in my own snippet of google maps I had a revelation. I finally understand how google makes money off the data it collects. Its been explained to me before but actually being billed for pulling up by filtering through an immense database really clarified things. 
This may seem a bit harsh to the average developer but it actually isn't when you also consider they provide each user with $300 of credit that resets every month. Considering the "Places API" that I use in this project cost me about .017 foor each use ($17 for 1000 runs), I'd say they give programmers a good enough cushion to test their ideas out before they actually need to start paying. This also has the effect of filtering out everyday developers with full fledged companies that utilize google databases everyday in the tens of thousands. 

After generating and API key, you have to call the map in your body tag:

```html
    <script
      src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&callback=initAutocomplete&libraries=places&v=weekly"
      async
    ></script>

```
From there the instructions to apply the API to my code was pretty straightforward, you can learn more about the specific one I used [here](https://developers.google.com/maps/documentation/javascript/examples/places-searchbox#maps_places_searchbox-html) <br><br>
The only thing I wanted to implement now was a way to center the map using the browser's location data. I wrote the following code into the  <span style="color:aqua;font-weight:bold">initAutoComplete()</span> function to get this to work:

```javascript
  if (navigator.geolocation) {
            console.log('geolocation is here');

            navigator.geolocation.getCurrentPosition((loc) => {
                location.lat = loc.coords.latitude;
                location.lng = loc.coords.longitude;

                //write the new map
                map = new google.maps.Map(document.getElementById('map'), options);

            },
                (err) => {
                    console.log('user clicked no');
                    map = new
                        google.maps.Map(document.getElementById('map'), options);

                })
        } else {
            console.log('geolocation not supported :(');
            map = new
                google.maps.Map(document.getElementById('map'), options);
        }
```

The if statement checks to see if the browser has enabled geolocation support. If they "allow location" the statement continues to <span style="color:aqua;font-weight:bold">.getCurrentPosition</span> and set the longitude and latitude equal to the initial coordinates the map was centered on. If they don't allow location, the console will print <span style="color:aqua;font-weight:bold">"user clicked no"</span> and start the map centered at its default location. In the case that the browser simply doesn't support geolocation at all, it will center the map by default as well. 

Now my user will be able to start searching for nearby cafes without having to type in their current position. 

<h2>The Form and Buttons</h2>

This part was much harder than I expected. Looking back the process and set up was actually quite simple but man oh man did I learn how to utilize the padding attribute in css. In order to redirect the user to the section of the page relevant to them I had to figure out how to read their input and respond accordingly. 

```javascript
function ColdOrHot() {
    var experience = document.getElementById('exp');
    var check1 = document.getElementById('check1');
    var check2 = document.getElementById('check2');
    var button = document.getElementById('findcoffeebutton');
    var form = document.getElementById('myform')

    if (check1.checked == true && check2.checked == false) {
        console.log(check1.checked)
        window.location.hash = 'coldcoffeerec'
    }
    else if (check2.checked == true && check1.checked == false) {
        console.log(check2.checked)
        window.location.hash = 'hotcoffeerec'
    }
    else if (check1.checked && check2.checked == true) {

        window.location.hash = 'hotcoffeerec'
    }
    form.reset();

}

```
The hot or cold on the form are check boxes so I named them <span style="color:aqua;font-weight:bold">check1</span> and <span style="color:aqua;font-weight:bold">check2</span> respectively. I start by assigning variables to specific DOM Elements on the page. My if statement then checks to see if either of the checks are checked returning a <span style="color:aqua;font-weight:bold">window.location.hash</span> command that directs the user to a location specified by the id's I've assigned on the page. 

```javascript
function LessOn() {
    var experience = document.getElementById('exp');
    var rad1 = document.getElementById('rad1');
    var rad2 = document.getElementById('rad2');
    var rad3 = document.getElementById('rad3');
    var button = document.getElementById('lessbutton');
    var form = document.getElementById('myform')

    if (radio1.checked == true) {
        console.log(radio1.checked)
        window.location.hash = 'beginnerinfo'
    }
    else if (radio2.checked == true) {
        console.log(radio2.checked)
        window.location.hash = 'mediuminfo'
    }
    else if (radio3.checked == true) {
        console.log(radio3.checked)
        window.location.hash = 'expertinfo'
    }
    form.reset();
}
```
I had several different ideas for this button, in fact the input started off as a textbox that took the following arguments: <span style="color:aqua;font-weight:bold">"beginner" "intermediate" "expert"</span>. After figuring out how to make that mess work, I realized that type of interaction is just far better suited for radio buttons to handle. The function does the same thing the check boxes but just includes an extra button here. Lastly the <span style="color:aqua;font-weight:bold">form.reset()</span> statement here serves us by resetting the inputs after the button is clicked. This way, if the user wishes to scroll back up and navigate again they can without having to reload the entire page. 

<h2>Conclusion</h2>
I had fun making this project. In the end it taught me more html and css than anything else. Also interacting with forms and learning how to link buttons to functions was a major lesson I can see being useful in the future. 