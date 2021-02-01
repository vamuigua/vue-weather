<template>
  <div
    id="app"
    :class="(typeof weather.main != 'undefined', currentBackground())"
  >
    <main>
      <div class="app-title">
        <h1>Vue Weather ⛅</h1>
        <small
          ><a href="http://github.com/vamuigua" target="_blank"
            >By Victor Allen</a
          ></small
        >
      </div>

      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Enter city of interest..."
          v-model="query"
          @keyup.enter="fetchWeather"
        />

        <div class="search-button">
          <button @click="fetchWeather" :disabled="isSearching">Search</button>
        </div>
      </div>

      <div class="alert" v-if="error_msg">
        <span
          class="closebtn"
          onclick="this.parentElement.style.display='none';"
          >&times;</span
        >
        <strong>Error!</strong> {{ error_msg }}
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp" @click="changeTempUnits">
            {{ temp }}
          </div>
          <div class="weather-details">
            <div class="weather-img">
              <img :src="weather_icon" alt="weather_img" />
            </div>
            <div class="weather-description">
              {{ weather.weather[0].description }}
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      api_key: process.env.VUE_APP_API_KEY,
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      isSearching: false,
      weather_icon: "",
      temp: "",
      isMetric: true,
      error_msg: "",
      dayTime: "",
    };
  },
  methods: {
    fetchWeather() {
      if (this.query != "") {
        this.error_msg = "";
        this.isSearching = true;
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&appid=${this.api_key}`
        )
          .then((res) => {
            return res.json();
          })
          .then(this.setResuts);
        this.query = "";
      } else {
        this.error_msg = "Please provide a city name.";
      }
    },
    setResuts(results) {
      if (results.cod == "200") {
        this.weather = results;
        this.temp = Math.round(results.main.temp) + "°c";
        this.weather_icon = `http://openweathermap.org/img/wn/${results.weather[0].icon}@2x.png`;
        this.dayTime = this.weather.weather[0].icon.slice(-1);
      } else {
        this.error_msg = results.message;
      }
      this.isSearching = false;
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    },
    changeTempUnits() {
      this.isMetric = !this.isMetric;
      if (this.isMetric) {
        this.temp = Math.round(this.weather.main.temp) + "°c";
      } else {
        const fahrenheit = Math.round((this.weather.main.temp * 9) / 5 + 32);
        this.temp = fahrenheit + "°F";
      }
    },
    currentBackground() {
      if (this.dayTime == "d") {
        return this.weather.main.temp >= 16 ? "warm-day" : "cold-day";
      } else if (this.dayTime == "n") {
        return this.weather.main.temp >= 16 ? "warm-night" : "cold-night";
      }
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "montserrat", sans-serif;
}

#app {
  background-image: url("./assets/warm-night-bg.jpeg");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm-day {
  background-image: url("./assets/warm-day-bg.jpeg");
}

#app.warm-night {
  background-image: url("./assets/warm-night-bg.jpeg");
}

#app.cold-day {
  background-image: url("./assets/cold-day-bg.jpeg");
}
#app.cold-night {
  background-image: url("./assets/cold-night-bg.jpeg");
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}

.app-title {
  margin: 5px auto;
  color: #fff;
  font-size: 25px;
  font-weight: 500;
  text-align: center;
  text-shadow: 2px 3px rgba(0, 0, 0, 0.5);
}

.app-title small a {
  text-decoration: none;
  color: #fff;
  transition: 0.4s;
}

.app-title small a:hover {
  padding: 5px;
  margin: 10px auto;
  border-radius: 5px;
  color: black;
  text-shadow: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}
.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}
.search-box .search-bar::placeholder {
  color: #2b2a2a;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.search-box .search-button {
  margin: 5px auto;
  text-align: center;
  padding: 10px 20px;
  display: block;
}

.search-box button {
  font-size: 1.5em;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.5);
  color: white;
  padding: 5px;
  border-radius: 10px;
  appearance: none;
  border: none;
  outline: none;
  width: 50%;
  box-shadow: 4px 4px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 10px;
  cursor: pointer;
  transition: 0.4s;
}

.search-box button:hover {
  background-color: rgb(255, 255, 255);
  color: black;
}
.search-box button:disabled {
  background-color: rgba(255, 255, 255, 0.1);
  cursor: not-allowed;
}

.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}
.weather-box {
  text-align: center;
}
.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .temp:hover {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  cursor: pointer;
}

.weather-box .weather-details {
  display: inline-block;
  box-sizing: border-box;
  margin-left: 20px;
}

.weather-box .weather-description {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.errors p {
  color: #fff;
  font-size: 35px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.alert {
  width: 80%;
  margin: 10px auto;
  padding: 20px;
  color: white;
  text-align: center;
  text-transform: capitalize;
  background-color: rgb(244, 67, 54);
  text-shadow: 1px 2px rgba(0, 0, 0, 0.25);
  box-shadow: 3px 3px rgba(244, 67, 54, 0.25);
}

.closebtn {
  margin-left: 15px;
  color: white;
  font-weight: bold;
  float: right;
  font-size: 40px;
  line-height: 20px;
  cursor: pointer;
  transition: 0.3s;
}

.closebtn:hover {
  color: black;
}
</style>
