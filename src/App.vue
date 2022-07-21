<template>
  <div id="app" :class="weather.cod === 200 && assignBackground()">
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search...."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>
      <div class="weather-wrap" v-if="weather.cod === 200">
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">
            {{ currentDateTime() }}
          </div>
        </div>
        <div class="weather-box">
          <div class="temprature">
            <p>{{ Math.round(weather.main.temp) }}°C</p>
            <img :src="icon + weather.weather[0].icon + '@4x.png'" />
          </div>
          <div class="weather">
            {{ weather.weather[0].description }}, feels like:
            {{ Math.round(weather.main.feels_like) }}°C
          </div>
        </div>
      </div>
      <div class="error" v-else-if="weather.cod === '404'">City not found.</div>
    </main>
  </div>
</template>

<script>
import moment from "moment";

export default {
  name: "App",
  data() {
    return {
      api_key: "fe159c9233f7b12c96110e15952e97e0",
      base_url: "https://api.openweathermap.org/data/2.5/",
      icon: "https://openweathermap.org/img/wn/",
      query: "",
      weather: {},
    };
  },
  computed: {},
  methods: {
    fetchInitialWeather() {
      let longitude;
      let latitude;
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(async (position) => {
          longitude = position.coords.longitude;
          latitude = position.coords.latitude;
          let response = await fetch(
            `${this.base_url}weather?lat=${latitude}&lon=${longitude}&units=metric&APPID=${this.api_key}`
          );
          let json = await response.json();
          this.weather = await json;
        });
      }
    },
    async fetchWeather(event) {
      if (event.key == "Enter") {
        let response = await fetch(
          `${this.base_url}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        );
        let json = await response.json();
        this.weather = await json;
        console.log(this.weather.cod);
      }
    },
    currentDateTime() {
      return moment().format("MMMM Do YYYY");
    },
    assignBackground() {
      let temperature = this.weather.main.temp;
      if (temperature <= 10) {
        return "cold";
      } else if (temperature <= 20) {
        return "cool";
      } else if (temperature <= 30) {
        return "warm";
      } else if (temperature > 30) {
        return "hot";
      }
    },
  },
  beforeMount() {
    this.fetchInitialWeather();
  },
};
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: "Century Gothic";
}

main {
  min-height: 100vh;
  padding: 24px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}

#app {
  background-color: black;
  background-position: bottom;
  background-size: cover;
  transition: 0.4s;
}

#app.cold {
  background-image: url("./assets/cold.jpg");
}

#app.cool {
  background-image: url("./assets/cool.jpg");
}

#app.warm {
  background-image: url("./assets/warm.jpg");
}

#app.hot {
  background-image: url("./assets/hot.jpg");
}

.search-box {
  align-items: center;
  display: flex;
  justify-content: center;
  margin: 20px 0px;
  width: 100%;
}

.search-bar {
  appearance: none;
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  border: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  color: black;
  display: block;
  font-size: 20px;
  outline: none;
  padding: 16px;
  transition: 0.4s;
  width: 60%;
}

.search-bar:focus {
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
}

.weather-wrap {
  padding: 20px 20px;
}

.location {
  color: whitesmoke;
  font-size: 28px;
  text-align: center;
  text-shadow: 2px 4px rgba(0, 0, 0, 0.25);
}

.date {
  color: whitesmoke;
  font-size: 20px;
  font-style: italic;
  font-weight: 280;
  text-align: center;
}

.weather-box {
  align-items: center;
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: center;

  transition: 0.4s;
}

.temprature {
  align-items: center;
  color: whitesmoke;
  display: flex;
  font-size: 80px;
  justify-content: center;
  text-shadow: 2px 2px rgba(0, 0, 0, 0.25);
}

.temprature img {
  filter: drop-shadow(2px 4px rgba(0, 0, 0, 0.25));
}

.weather {
  color: whitesmoke;
  font-size: 28px;
  font-style: italic;
  text-shadow: 2px 2px rgba(0, 0, 0, 0.25);
}

.error {
  color: whitesmoke;
  font-size: 28px;
  font-style: italic;
  text-align: center;
  text-shadow: 2px 2px rgba(0, 0, 0, 0.25);
}

@media screen and (max-width: 820px) {
  .search-bar {
    width: 100%;
  }
}
</style>
