# WeatherApp-using-html-css-and-javascript
In this weather app, you can get the weather details of a particular city by entering the city name or you can also get your current location weather details by clicking on the “Get Device Location” button.     const wrapper = document.querySelector(".wrapper")  inputField.addEventListener
![project sample photo 1](https://user-images.githubusercontent.com/97597920/194937406-6171d040-6b84-4f0a-baae-0995ea9dfac1.jpg)
![project sample photo 2](https://user-images.githubusercontent.com/97597920/194937416-9d474ff1-cec2-4df9-ac92-be0261e7d701.jpg)
Where To Find Weather Icons and Weather UI Kits 
Before we start with the weather API JavaScript code, we’ll need weather app icons. It’s worth noting that OpenWeatherMap comes with its own icon set and we’ll take a look at those. However, we’ll go one step further and use some custom ones. 

Now, if you’re a web designer or work on multiple web design projects, Envato Elements is the best option for you.

For a low monthly fee, this subscription-based marketplace gives you unlimited access to everything you’ll need for your projects.

Here we’ll review some top weather icon vector sets and weather UI kits. Keep browsing Envato Elements weather UI kit library for more!

1. Weather Icons and Font
![Weather Icons and Font](https://user-images.githubusercontent.com/97597920/194937802-70f38306-7126-4766-b517-9b390c67b851.jpg)
If you like simple weather app icons, these are for you. This weather icon vector set comes with 32 vector icons and a Forecast font. This complete weather icon vector set includes 32 SVG files as well as HTML and CSS references.

2. UICON Weather Icons
![UICON Weather Icons](https://user-images.githubusercontent.com/97597920/194937900-5606c340-4ee0-4aa9-8232-b98fc3dbe190.jpg)
This weather icon vector set features a modern and fresh look. There are 12 weather app icons in two color versions. All weather icons are crafted in EPS with a minimal design.

5. Add the JavaScript
At this point, we’re ready to build the core functionality of our weather app. Let’s do it!

On Form Submission
Each time a user submits the form by pressing the Enter key or the Submit button, we’ll do two things:

Stop the form from submitting, hence prevent reloading the page.
Grab the value which is contained in the search field.
Here’s the starting code:

1
2
3
4
5
6
const form = document.querySelector(".top-banner form");
 
form.addEventListener("submit", e => {
  e.preventDefault();
  const inputVal = input.value;
});
Next, we’ll check to see whether there are list items (cities) inside the second section. 

Perform an AJAX Request
We’ll start with the assumption that the list is empty. That said, it has never run any AJAX request in the past. In such a case, we’ll execute a request to the OpenWeatherMap API and pass the following parameters:

The city name (e.g. athens) or the comma-separated city name along with the country code (e.g. athens,gr) which will be the value of the search field
The API key. Again, you should use your own key to avoid unexpected errors due to API call limits.
The unit of temperature for the requested city. In our case, we’ll go with Celcius.
With all the above in mind, by following the API documentation, our request URL should look something like this:

1
2
3
4
5
6
const apiKey = "YOUR_OWN_KEY";
const inputVal = input.value;
 
...
 
const url = `https://api.openweathermap.org/data/2.5/weather?q=${inputVal}&appid=${apiKey}&units=metric`;
To perform the AJAX request, we have a lot of options. We can use the plain old XMLHttpRequest API, the newer Fetch API, or even a JavaScript library like jQuery and Axios. For this example, we’ll go with the Fetch API. 

To grab the desired data, we have to do the following things:

Pass the URL we want to access to the fetch() method. 
This method will return a Promise containing the response (a Response object). But this won’t be the actual response, just an HTTP response. To grab the response data in the desired JSON format (this is the default data format of OpenWeatherMap), we’ll use Response object’s json() method.
This method will return another Promise. When it’s fulfilled, the data will be available for manipulation.
If for some reason the request is unsuccessful, a corresponding message will appear on the screen.
So, our AJAX request would look something like this:

01
02
03
04
05
06
07
08
09
10
...
 
 fetch(url)
  .then(response => response.json())
  .then(data => {
    // do stuff with the data
  })
  .catch(() => {
    msg.textContent = "Please search for a valid city 😩";
  });
Tip: Instead of chaining then()s, we could have used the newer and more readable async/await approach for the AJAX request.
Build the List Item Component
With the AJAX request in place, each time we type a city in the search field, the API will return its weather data, if they are available. Our job now is to collect only the data that we need, then create the associated list item and, lastly, append it to the unordered list.

Here’s the code responsible for this job:

01
02
03
04
05
06
07
08
09
10
11
12
13
14
15
16
17
18
19
20
21
const { main, name, sys, weather } = data;
const icon = `https://openweathermap.org/img/wn/${
  weather[0]["icon"]
}@2x.png`;
 
const li = document.createElement("li");
li.classList.add("city");
const markup = `
  <h2 class="city-name" data-name="${name},${sys.country}">
    <span>${name}</span>
    <sup>${sys.country}</sup>
  </h2>
  <div class="city-temp">${Math.round(main.temp)}<sup>°C</sup>
  </div>
  <figure>
    <img class="city-icon" src=${icon} alt=${weather[0]["main"]}>
    <figcaption>${weather[0]["description"]}</figcaption>
  </figure>
`;
li.innerHTML = markup;
list.appendChild(li);
There are two things here we have to discuss:

If you look again at the response visualization above, you’ll notice that the API returns an icon code (e.g. "50d") which holds the current weather condition for the target city. Based on this code, we’re able to construct the icon URL and display it in the card via the img tag.
Inside the .city-name element of each list item, we’ll append the data-name attribute with value the cityName,countryCode (e.g. madrid,es). Later we’ll use this value to prevent duplicate requests.





const wrapper = document.querySelector(".wrapper"),
inputPart = document.querySelector(".input-part"),
infoTxt = inputPart.querySelector(".info-txt"),
inputField = inputPart.querySelector("input"),
locationBtn = inputPart.querySelector("button"),
weatherPart = wrapper.querySelector(".weather-part"),
wIcon = weatherPart.querySelector("img"),
arrowBack = wrapper.querySelector("header i");

let api;

inputField.addEventListener("keyup", e =>{
    if(e.key == "Enter" && inputField.value != ""){
        requestApi(inputField.value);
    }
});

locationBtn.addEventListener("click", () =>{
    if(navigator.geolocation){
        navigator.geolocation.getCurrentPosition(onSuccess, onError);
    }else{
        alert("Your browser not support geolocation api");
    }
});

function requestApi(city){
    api = `https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&appid=your_api_key`;
    fetchData();
}

function onSuccess(position){
    const {latitude, longitude} = position.coords;
    api = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&units=metric&appid=your_api_key`;
    fetchData();
}

function onError(error){
    infoTxt.innerText = error.message;
    infoTxt.classList.add("error");
}

function fetchData(){
    infoTxt.innerText = "Getting weather details...";
    infoTxt.classList.add("pending");
    fetch(api).then(res => res.json()).then(result => weatherDetails(result)).catch(() =>{
        infoTxt.innerText = "Something went wrong";
        infoTxt.classList.replace("pending", "error");
    });
}

function weatherDetails(info){
    if(info.cod == "404"){
        infoTxt.classList.replace("pending", "error");
        infoTxt.innerText = `${inputField.value} isn't a valid city name`;
    }else{
        const city = info.name;
        const country = info.sys.country;
        const {description, id} = info.weather[0];
        const {temp, feels_like, humidity} = info.main;

        if(id == 800){
            wIcon.src = "icons/clear.svg";
        }else if(id >= 200 && id <= 232){
            wIcon.src = "icons/storm.svg";  
        }else if(id >= 600 && id <= 622){
            wIcon.src = "icons/snow.svg";
        }else if(id >= 701 && id <= 781){
            wIcon.src = "icons/haze.svg";
        }else if(id >= 801 && id <= 804){
            wIcon.src = "icons/cloud.svg";
        }else if((id >= 500 && id <= 531) || (id >= 300 && id <= 321)){
            wIcon.src = "icons/rain.svg";
        }
        
        weatherPart.querySelector(".temp .numb").innerText = Math.floor(temp);
        weatherPart.querySelector(".weather").innerText = description;
        weatherPart.querySelector(".location span").innerText = `${city}, ${country}`;
        weatherPart.querySelector(".temp .numb-2").innerText = Math.floor(feels_like);
        weatherPart.querySelector(".humidity span").innerText = `${humidity}%`;
        infoTxt.classList.remove("pending", "error");
        infoTxt.innerText = "";
        inputField.value = "";
        wrapper.classList.add("active");
    }
}

arrowBack.addEventListener("click", ()=>{
    wrapper.classList.remove("active");
});
