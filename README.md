// Custom Multi-Info Widget

// Function to fetch weather data
async function fetchWeatherData() {
  const location = 'city, country initials'; // Your location
  const apiKey = 'Enter your API key'; // Your OpenWeatherMap API Key
  const apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${encodeURIComponent(location)}&appid=${apiKey}&units=metric`;
  const request = new Request(apiUrl);
  const response = await request.loadJSON();
  return response;
}

// Function to fetch AQI data
async function fetchAQIData() {
  const aqiApiKey = 'Enter your api key'; // Your AQICN API Key
  const aqiApiUrl = `https://api.waqi.info/feed/ENTER YOUR CITY NAME HERE/?token=${aqiApiKey}`;
  const aqiRequest = new Request(aqiApiUrl);
  const aqiResponse = await aqiRequest.loadJSON();
  return aqiResponse.data;
}
// Function to create and display the widget
async function createCustomWidget() {
  // Get current location information
  const locationData = await Location.current();
  const latitude = locationData.latitude;
  const longitude = locationData.longitude;

  // Fetch weather data
  const weatherData = await fetchWeatherData();
  const temperature = Math.round(weatherData.main.temp);
  const weatherDescription = weatherData.weather[0].description;

  // Fetch AQI data
  const aqiData = await fetchAQIData();
  const aqiValue = aqiData.aqi;
  
  // Get current time in the specified time zone
  const currentTime = new Date().toLocaleTimeString('en-US', { timeZoneName: 'short' });

  // Calculate days left in the year
  function daysLeftInYear() {
    const today = new Date();
    const endOfYear = new Date(today.getFullYear(), 11, 31);
    const timeDiff = endOfYear - today;
    return Math.ceil(timeDiff / (1000 * 3600 * 24));
  }
  const daysLeft = daysLeftInYear();

  // Get device model
  const deviceModel = Device.model();

  // Fetch battery level (as a floating-point number)
  const batteryLevel = Device.batteryLevel();
  // Display the battery level with two decimal places for accuracy
  const batteryPercentage = (batteryLevel * 100).toFixed(2);

  // Create a widget
  let widget = new ListWidget();
  const weatherText = widget.addText(`Weather: ${temperature}°C, ${weatherDescription}`);
  weatherText.textColor = Color.black(); // Black text color
  const aqiText = widget.addText(`AQI: ${aqiValue}`);
  aqiText.textColor = Color.black(); // Black text color
  const timeText = widget.addText(`Time: ${currentTime}`);
  timeText.textSize = 10; // Adjust the text size
  timeText.textColor = Color.black(); // Black text color
  const latitudeText = widget.addText(`Latitude: ${latitude}`);
  latitudeText.textSize = 10; // Adjust the text size
  latitudeText.textColor = Color.black(); // Black text color
  const longitudeText = widget.addText(`Longitude: ${longitude}`);
  longitudeText.textSize = 10; // Adjust the text size
  longitudeText.textColor = Color.black(); // Black text color
  const modelText = widget.addText(`Device Model: ${deviceModel}`);
  modelText.textSize = 10; // Adjust the text size
  modelText.textColor = Color.black(); // Black text color
  const batteryText = widget.addText(`Battery: ${batteryPercentage}%`);
  batteryText.textSize = 10; // Adjust the text size
  batteryText.textColor = Color.black(); // Black text color
  const daysLeftText = widget.addText(`Days Left in Year: ${daysLeft}`);
  daysLeftText.textSize = 10; // Adjust the text size
  daysLeftText.textColor = Color.black(); // Black text color

  // Set widget background with rainbow stripes
  widget.backgroundImage = drawRainbowBackground();

  // Display the widget
  if (config.runsInWidget) {
    Script.setWidget(widget);
  } else {
    widget.presentMedium();
  }

  Script.complete();
}

// Function to draw rainbow background
function drawRainbowBackground() {
  const ctx = new DrawContext();
  const size = new Size(400, 200); // Adjust the size as needed
  ctx.size = size;
  ctx.opaque = false;
  ctx.respectScreenScale = true;
  const width = size.width;
  const height = size.height;

  // Define the number of rainbow stripes and their colors
  const numStripes = 7; // Rainbow colors
  const rainbowColors = ['#FF0000', '#FF7F00', '#FFFF00', '#00FF00', '#0000FF', '#4B0082', '#9400D3']; // Rainbow colors
  const stripeWidth = width / numStripes;

  for (let i = 0; i < numStripes; i++) {
    const x = i * stripeWidth;
    const stripeColor = rainbowColors[i];
    ctx.setFillColor(new Color(stripeColor));
    ctx.fillRect(new Rect(x, 0, stripeWidth, height));
  }

  return ctx.getImage();
}

// Run the script to create the widget
await createCustomWidget()