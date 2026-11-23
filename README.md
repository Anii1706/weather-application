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
