<template>
  <div class="container">
    <div class="header">
      <div class="location">
        <img src="./assets/Vector.png" />
        <div class="location-city">HỒ CHÍ MINH</div>
      </div>
      <div class="notication">
        <img src="./assets/noti.png" />
      </div>
    </div>
    <div class="content">
      <div class="content-img">
        <div v-if="humidity > 50">
          <img src="./assets/sunclose.png" />
        </div>
        <div v-else>
          <img src="./assets/sun.png" />
        </div>

        <!-- <img src="./assets/sun.png" alt="" /> -->
      </div>
      <div class="content-temp">{{ Math.floor(currentTemp) }}<sup>o</sup></div>
      <div>{{ dayday }}</div>
      <div class="content-humidity">
        <div class="content-humidity-max">Max: {{ maxDay }}<sup>o</sup></div>
        <div class="content-humidity-min">Min: {{ minDay }}<sup>o</sup></div>
      </div>
      <div class="content-detail bgc">
        <div class="content-detail-humidity">
          <img src="./assets/humidity.png" alt="" />
          {{ rain }} %
        </div>

        <div class="content-detaFil-temp">
          <img src="./assets/wind.png" alt="" />
          {{ humidity }} %
        </div>

        <div class="content-detail-wind">
          <img src="./assets/wind.png" alt="" />
          {{ wind }} km/h
        </div>
      </div>
    </div>
    <div class="weather bgc">
      <div class="weather-header">
        <div style="font-weight: bold" class="weather-header-day">Today</div>
        <div class="weather-header-time">
          {{ time }}
        </div>
      </div>
      <div>
        <div style="overflow-y: hidden">
          <!-- <tr>
            <td
              v-for="(n, index) in temperature"
              :key="index"
              class="weather-content"
            >
              <p>{{ n }} <sup>o</sup>C</p>
            </td>
            <td v-for="(hour, index) in img" :key="index">
              <div v-if="hour < 6 && hour > 18">
                <img src="./assets/unionf.png" />
              </div>
              <div v-else>
                <img src="./assets/night.png" />
              </div>
            </td>
            <td v-for="(timer, idx) in hour" :key="idx">
              {{ timer.slice(11, 17) }}
            </td>
          </tr> -->

          <div class="weather-content-list">
            <div
              v-for="(n, index) in temperature"
              :key="index"
              class="weather-content"
            >
              <p>{{ n }} <sup>o</sup>C</p>
            </div>
          </div>
          <div style="display: flex">
            <div v-for="(hours, index) in img" :key="index">
              <div v-if="hours > 6 && hours < 18">
                <img src="./assets/unionf.png" />
              </div>
              <div v-else>
                <img src="./assets/night.png" />
              </div>
              <!-- <img class="weather-content-img" :src="image" alt="" />
            <img class="weather-content-img" src="./assets/unionf.png" alt="" /> -->
            </div>
          </div>
          <span class="time">
            <p v-for="(timer, idx) in hour" :key="idx">
              {{ timer.slice(11, 17) }}
            </p>
          </span>
        </div>
      </div>
    </div>
    <div class="footer bgc">
      <div class="footer-header">
        <p style="font-weight: bold">Next Forecast</p>
        <img src="./assets/calendar.png" />
      </div>

      <table class="footer-sum">
        <tr
          v-for="(day, i) in nameday"
          :key="i"
          @click="
            getTemp(min[i], max[i], cloudcoverLow[i], dayweek[i], timeday[i])
          "
          class="day"
        >
          <td>
            <p>{{ day }}</p>
          </td>
          <td><img class="img" src="./assets/rain.png" alt="" /></td>
          <td>
            <p>{{ min[i] }}<sup>oC</sup></p>
          </td>
          <td>
            <p>{{ max[i] }}<sup>oC</sup></p>
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { format } from "date-fns";

//import addYears from "date-fns/esm/addYears/index";
//import { ref } from "vue";

export default {
  name: "App",
  data() {
    return {
      currentTemp: "",
      temperature: "",
      wind: "",
      rain: "",
      humidity: "",
      time: "",
      hour: "",
      nameday: [],
      min: "",
      max: "",
      img: [],
      latitude: 10,
      minDay: 0,
      maxDay: 0,
      cloudcoverLow: "",
      dayday: "",
      dayweek: "",
      tempWeek: "",
      timeday: "",
    };
  },

  methods: {
    getWeather() {
      let url =
        "https://api.open-meteo.com/v1/forecast?latitude=10.82&longitude=106.63&hourly=temperature_2m,cloudcover_low&daily=weathercode,temperature_2m_max,temperature_2m_min,sunrise,precipitation_sum,rain_sum,showers_sum,windspeed_10m_max,windgusts_10m_max,shortwave_radiation_sum&timezone=Asia%2FBangkok&start_date=2022-12-05&end_date=2022-12-05";
      axios
        .get(url)
        .then((response) => {
          const data = response.data;
          const hourTemp = data.hourly.temperature_2m;
          this.temperature = hourTemp;
          this.minDay = Math.min(...hourTemp);
          this.maxDay = Math.max(...hourTemp);
          this.currentTemp = Math.abs(
            (Math.max(...hourTemp) + Math.min(...hourTemp)) / 2
          ); // tao bien luu data, khong dung "response" nhieu lan
          this.wind = Math.max(...data.daily.windspeed_10m_max);
          this.rain = Math.min(...data.hourly.cloudcover_low);
          this.humidity = data.daily.weathercode[0];
          this.time = format(new Date(data.daily.time[0]), "MMM, d");
          console.log(this.time);
          this.hour = data.hourly.time;
          this.dayday = data.daily.time[0];
          const arr = data.hourly.time;
          arr.map((data) => {
            this.img.push(parseInt(format(new Date(data), "	H")));
          });
        })
        .catch((error) => {
          console.log(error);
        });
    },
    getData() {
      let url =
        "https://api.open-meteo.com/v1/forecast?latitude=10.82&longitude=106.63&hourly=temperature_2m,cloudcover_low&daily=weathercode,temperature_2m_max,temperature_2m_min,rain_sum&timezone=Asia%2FBangkok&start_date=2022-12-05&end_date=2022-12-12";
      axios
        .get(url)
        // , {
        //   params: {
        //     latitude: this.latitude,
        //   },
        // })
        .then((res) => {
          console.log(res.data);
          //const datefns = require("date-fns");
          const arr = res.data.daily.time;

          arr.map((date) => {
            this.nameday.push(format(new Date(date), "	EEEE"));
          });

          this.min = res.data.daily.temperature_2m_min;
          this.max = res.data.daily.temperature_2m_max;
          this.cloudcoverLow = res.data.daily.weathercode;
          this.dayweek = res.data.daily.time;
          this.tempWeek = res.data.hourly.temperature_2m;
          console.log(new Date(res.data.daily.time));
          this.timeday = arr;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    getTemp(min, max, cloudcoverLow, dayweek, timeday) {
      this.currentTemp = (min + max) / 2;
      this.minDay = min;
      this.maxDay = max;
      this.dayday = dayweek;
      this.time = timeday;
      //this.temperature = tempWeek;
      this.humidity = cloudcoverLow;
      this.humidity > 50
        ? (document.querySelector(".container").style.background =
            " linear-gradient(167.44deg, #29B2DD 0%, #33AADD 47.38%, #2DC8EA 100%)")
        : (document.querySelector(".container").style.background =
            "linear-gradient(167.44deg, #08244F 0%, #134CB5 47.38%, #0B42AB 100%)");
    },
  },
  beforeMount() {
    this.getWeather();
    this.getData();
  },
};
</script>
<style>
.container {
  width: 423px;
  height: 100%;
  padding: 44px 40px;
  background: linear-gradient(
    167.44deg,
    #29b2dd 0%,
    #33aadd 47.38%,
    #2dc8ea 100%
  );
  color: #fff;
  border-radius: 40px;
  font-family: Arial, "Helvetica Neue", Helvetica, sans-serif;
  overflow: hidden;
}
@media screen and (max-width: 800px) {
  .container {
    width: auto;
    height: auto; /* The width is 100%, when the viewport is 800px or smaller */
  }
}
.day:hover {
  cursor: pointer;
}
.location-drop {
  width: 5%;
  height: 40%;
}
.header {
  display: flex;
  justify-content: space-between;
}
.footer-sum {
  display: block;
}
.location {
  gap: 20px;
  display: flex;
  width: 100%;
  align-items: center;
  color: #fff;
}
.content {
  text-align: center;
}
.content-temp {
  font-size: 64px;
}
.content-humidity {
  display: flex;
  gap: 25px;
  justify-content: center;
  padding-bottom: 30px;
}
.content-detail {
  display: flex;
  justify-content: space-between;

  background: rgb(0, 19, 46, 0.7);
  border-radius: 20px;
  padding: 14px 40px;
}
.weather {
  background: rgb(0, 19, 46, 0.7);
  border-radius: 20px;
  padding: 12px 16px;
  margin: 20px 0;
}
.weather-header {
  display: flex;
  justify-content: space-between;
}

.time {
  margin: 0 44px;
  display: flex;
  gap: 88px;
}
.time p {
  margin: 0;
}
.weather-content-list {
  display: flex;
  margin: 25px 46px;
  gap: 79px;
}
.weather-content {
  text-align: center;
  position: relative;
}
.weather-content p {
  margin: 0;
  display: flex;
}
.footer {
  background: rgb(0, 19, 46, 0.7);
  border-radius: 20px;
  padding: 11px 14px;
  height: 300px;
  overflow: auto;
}
.footer-header {
  display: flex;
  justify-content: space-between;
}
.footer-header p {
  margin: 0;
}
.footer-sum {
  justify-content: space-between;
}
.footer-item {
  display: flex;
  justify-content: space-between;
}
.min-max {
  display: flex;
  line-height: 49px;
}
.min {
  padding: 0 20px;
}
td {
  padding: 0 15px;
}

::-webkit-scrollbar {
  width: 20px;
}

/* Track */
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 5px transparent;
  border-radius: 10px;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: transparent;
  border-radius: 10px;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: transparent;
}
</style>
