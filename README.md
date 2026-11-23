# weather-application
A responsive weather application using HTML, CSS, JavaScript and Open-Meteo API

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Weather Application</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <main class="weather-container">
    <h1>🌤 Weather App</h1>
    <p class="subtitle">Discover the weather anywhere in the world</p>

    <form id="search-form">
      <input
        type="text"
        id="city-input"
        placeholder="Enter city name..."
        required
      >
      <button type="submit">Search</button>
    </form>

    <p id="message" aria-live="polite">Search for a city to see its weather.</p>

    <section id="weather" class="weather-card" hidden>
      <h2 id="city-name"></h2>
      <div id="weather-icon" class="weather-icon">🌤️</div>
      <h3 id="temperature">--°C</h3>
      <p id="description">Weather condition</p>

      <div class="details">
        <div>
          <span>💧</span>
          <p>Humidity</p>
          <strong id="humidity">--%</strong>
        </div>
        <div>
          <span>💨</span>
          <p>Wind Speed</p>
          <strong id="wind">-- km/h</strong>
        </div>
      </div>
    </section>

    <section class="forecast-section">
      <h2>5-Day Forecast</h2>
      <div id="forecast" class="forecast-grid"></div>
    </section>

    <footer>
      Powered by Open-Meteo API
    </footer>
  </main>

  <script src="script.js"></script>
</body>
</html>

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  min-height: 100vh;
  background: linear-gradient(135deg, #153677, #4e085f);
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 25px;
  color: white;
}

.weather-container {
  width: 100%;
  max-width: 600px;
  text-align: center;
  background: rgba(255, 255, 255, 0.13);
  padding: 30px;
  border-radius: 22px;
  box-shadow: 0 10px 35px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(12px);
}

h1 {
  font-size: 32px;
  margin-bottom: 10px;
}

.subtitle {
  color: #e0e7ff;
  margin-bottom: 25px;
}

form {
  display: flex;
  gap: 10px;
}

input {
  flex: 1;
  min-width: 0;
  padding: 14px;
  border: none;
  border-radius: 10px;
  outline: none;
  font-size: 16px;
}

button {
  padding: 14px 20px;
  border: none;
  border-radius: 10px;
  background: #ffcc70;
  color: #222;
  font-weight: bold;
  cursor: pointer;
}

button:hover {
  background: #ffb938;
}

#message {
  margin: 18px 0;
  color: #e0e7ff;
}

.weather-card {
  background: rgba(255, 255, 255, 0.12);
  padding: 25px;
  border-radius: 18px;
  margin-top: 20px;
}

.weather-icon {
  font-size: 65px;
  margin: 12px;
}

#temperature {
  font-size: 45px;
}

#description {
  margin: 10px;
  font-size: 18px;
}

.details {
  display: flex;
  justify-content: space-around;
  margin-top: 25px;
  gap: 15px;
}

.details p {
  margin: 8px 0;
}

.forecast-section {
  margin-top: 30px;
}

.forecast-grid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 8px;
  margin-top: 15px;
}

.forecast-day {
  background: rgba(255, 255, 255, 0.13);
  border-radius: 12px;
  padding: 12px 5px;
  font-size: 13px;
}

.forecast-day span {
  display: block;
  margin: 8px 0;
  font-size: 24px;
}

footer {
  margin-top: 30px;
  color: #ddd;
  font-size: 13px;
}

[hidden] {
  display: none !important;
}

@media (max-width: 480px) {
  .weather-container {
    padding: 20px 14px;
  }

  h1 {
    font-size: 26px;
  }

  button {
    padding: 12px;
  }

  .forecast-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

const form = document.getElementById("search-form");
const cityInput = document.getElementById("city-input");
const message = document.getElementById("message");
const weatherCard = document.getElementById("weather");
const forecastContainer = document.getElementById("forecast");

function getWeatherInfo(code) {
  const conditions = {
    0: ["Clear Sky", "☀️"],
    1: ["Mainly Clear", "🌤️"],
    2: ["Partly Cloudy", "⛅"],
    3: ["Overcast", "☁️"],
    45: ["Foggy", "🌫️"],
    48: ["Rime Fog", "🌫️"],
    51: ["Light Drizzle", "🌦️"],
    53: ["Drizzle", "🌦️"],
    55: ["Heavy Drizzle", "🌧️"],
    61: ["Light Rain", "🌧️"],
    63: ["Rain", "🌧️"],
    65: ["Heavy Rain", "🌧️"],
    71: ["Light Snow", "🌨️"],
    73: ["Snow", "❄️"],
    75: ["Heavy Snow", "❄️"],
    80: ["Rain Showers", "🌦️"],
    81: ["Heavy Showers", "🌧️"],
    82: ["Violent Showers", "⛈️"],
    95: ["Thunderstorm", "⛈️"],
    96: ["Thunderstorm with Hail", "⛈️"],
    99: ["Severe Thunderstorm", "⛈️"]
  };

  return conditions[code] || ["Unknown", "🌡️"];
}

async function getWeather(city) {
  try {
    message.textContent = "Loading weather...";
    weatherCard.hidden = true;
    forecastContainer.innerHTML = "";

    // Find city coordinates
    const geoUrl = `https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(city)}&count=1&language=en&format=json`;

    const geoResponse = await fetch(geoUrl);

    if (!geoResponse.ok) {
      throw new Error("Unable to find location.");
    }

    const geoData = await geoResponse.json();

    if (!geoData.results || geoData.results.length === 0) {
      throw new Error("City not found. Please try another name.");
    }

    const location = geoData.results[0];
    const { latitude, longitude, name, country } = location;

    // Get current weather and five-day forecast
    const weatherUrl =
      `https://api.open-meteo.com/v1/forecast?latitude=${latitude}` +
      `&longitude=${longitude}` +
      `&current=temperature_2m,relative_humidity_2m,weather_code,wind_speed_10m` +
      `&daily=weather_code,temperature_2m_max,temperature_2m_min` +
      `&forecast_days=5&timezone=auto`;

    const response = await fetch(weatherUrl);

    if (!response.ok) {
      throw new Error("Unable to fetch weather data.");
    }

    const data = await response.json();

    displayWeather(name, country, data);
    message.textContent = "Weather updated successfully!";

  } catch (error) {
    message.textContent = error.message;
  }
}

function displayWeather(city, country, data) {
  const current = data.current;
  const info = getWeatherInfo(current.weather_code);

  document.getElementById("city-name").textContent =
    `${city}, ${country}`;

  document.getElementById("temperature").textContent =
    `${Math.round(current.temperature_2m)}°C`;

  document.getElementById("weather-icon").textContent = info[1];

  document.getElementById("description").textContent = info[0];

  document.getElementById("humidity").textContent =
    `${current.relative_humidity_2m}%`;

  document.getElementById("wind").textContent =
    `${current.wind_speed_10m} km/h`;

  weatherCard.hidden = false;

  displayForecast(data.daily);
}

function displayForecast(daily) {
  forecastContainer.innerHTML = "";

  daily.time.forEach((date, index) => {
    const info = getWeatherInfo(daily.weather_code[index]);

    const day = new Date(`${date}T12:00:00`)
      .toLocaleDateString("en-US", { weekday: "short" });

    const card = document.createElement("div");
    card.className = "forecast-day";

    const dayName = document.createElement("p");
    dayName.textContent = day;

    const icon = document.createElement("span");
    icon.textContent = info[1];

    const condition = document.createElement("p");
    condition.textContent = info[0];

    const temperature = document.createElement("strong");
    temperature.textContent =
      `${Math.round(daily.temperature_2m_max[index])}° / ` +
      `${Math.round(daily.temperature_2m_min[index])}°`;

    card.append(dayName, icon, condition, temperature);
    forecastContainer.appendChild(card);
  });
}

form.addEventListener("submit", (event) => {
  event.preventDefault();

  const city = cityInput.value.trim();

  if (city) {
    getWeather(city);
  }
});

// Load a default city when the application opens
getWeather("Delhi");
