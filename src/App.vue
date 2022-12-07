<template>
  <div class="container" ref="bgweb">
    <div class="header">
      <div class="location">
        <font-awesome-icon
          icon="fa-solid fa-location-dot"
          class="location-loca"
        />
        <div class="location-city">HỒ CHÍ MINH</div>
        <font-awesome-icon
          icon="fa-solid fa-chevron-down"
          class="location-drop"
        />
      </div>
      <div class="notication">
        <font-awesome-icon icon="fa-solid fa-bell" />
      </div>
    </div>
    <div class="content">
      <div ref="icon" class="content-img">
        <img src="./assets/sun.png" alt="" />
      </div>
      <div class="content-temp">{{ Math.floor(currentTemp) }}<sup>o</sup></div>
      <div class="content-humidity">
        <div class="content-humidity-max">
          Max: {{ Math.max(...temperature) }}<sup>o</sup>
        </div>
        <div class="content-humidity-min">
          Min: {{ Math.min(...temperature) }}<sup>o</sup>
        </div>
      </div>
      <div class="content-detail">
        <div class="content-detail-humidity">
          <img src="./assets/humidity.png" alt="" />
          {{ rain }} %
        </div>

        <div class="content-detaFil-temp">
          <img src="./assets/temp.png" alt="" />
          {{ humidity }} %
        </div>

        <div class="content-detail-wind">
          <img src="./assets/wind.png" alt="" />
          {{ wind }} km/h
        </div>
      </div>
    </div>
    <div class="weather">
      <div class="weather-header">
        <div style="font-weight: bold" class="weather-header-day">Today</div>
        <div class="weather-header-time">
          {{ time }}
        </div>
      </div>
      <div>
        <div style="overflow-y: hidden">
          <div class="weather-content-list">
            <div
              v-for="(n, index) in temperature"
              :key="index"
              class="weather-content"
            >
              <p>{{ n }} <sup>o</sup>C</p>

              <div>
                <img
                  class="weather-content-img"
                  src="./assets/unionf.png"
                  alt=""
                />
              </div>
            </div>
          </div>
          <div v-for="(image, index) in img" :key="index">
            {{ image }}
          </div>
          <span class="time">
            <p v-for="(timer, idx) in hour" :key="idx">
              {{ timer.slice(11, 17) }}
            </p>
          </span>
        </div>
      </div>
    </div>
    <div class="footer">
      <div class="footer-header">
        <p style="font-weight: bold">Next Forecast</p>
        <font-awesome-icon icon="fa-solid fa-calendar-days" />
      </div>

      <div class="footer-sum">
        <div style="line-height: 37px; padding: 11px 0">
          <div
            @click="gettemp"
            v-for="(day, i) in nameday"
            :key="i"
            style="display: flex"
          >
            <p>{{ day }}</p>
          </div>
        </div>
        <div>
          <div v-for="(day, i) in nameday" :key="i">
            <p>{{}}</p>
            <img class="img" src="./assets/rain.png" alt="" />
          </div>
        </div>
        <div class="min-max">
          <div class="min">
            <div v-for="(m, i) in min" :key="i">
              <p>{{ m }}<sup>oC</sup></p>
            </div>
          </div>
          <div>
            <div v-for="(n, i) in max" :key="i">
              <p>{{ n }}<sup>oC</sup></p>
            </div>
          </div>
        </div>
      </div>
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
      aa: "",
    };
  },

  methods: {
    getWeather() {
      let url =
        "https://api.open-meteo.com/v1/forecast?latitude=10.82&longitude=106.63&hourly=temperature_2m,cloudcover_low&daily=weathercode,temperature_2m_max,temperature_2m_min,sunrise,precipitation_sum,rain_sum,showers_sum,windspeed_10m_max,windgusts_10m_max,shortwave_radiation_sum&timezone=Asia%2FBangkok&start_date=2022-12-05&end_date=2022-12-05";
      axios
        .get(url)
        .then((response) => {
          this.temperature = response.data.hourly.temperature_2m;
          this.currentTemp = Math.abs(
            (Math.max(...response.data.hourly.temperature_2m) +
              Math.min(...response.data.hourly.temperature_2m)) /
              2
          );
          this.wind = Math.max(...response.data.daily.windspeed_10m_max);
          this.rain = Math.min(...response.data.hourly.cloudcover_low);
          this.humidity = Math.max(...response.data.hourly.cloudcover_low);
          this.time = format(new Date(response.data.daily.time[0]), "MMM, d");
          this.hour = response.data.hourly.time;
          // const arr = response.data.hourly.time;

          // arr.map((date) => {
          //   console.log(date);
          //   this.img.push(
          //     format(new Date(date), "	H") > 6 &&
          //       format(new Date(date), "	H") < 18
          //       ? (src = "./assets/unionf.png")
          //       : "hh"
          //   );
          // });
        })
        .catch((error) => {
          console.log(error);
        });
    },
    getData() {
      let url =
        "https://api.open-meteo.com/v1/forecast?latitude=10.82&longitude=106.63&hourly=temperature_2m,cloudcover_low&daily=temperature_2m_max,temperature_2m_min&timezone=Asia%2FBangkok&start_date=2022-12-05&end_date=2022-12-12";
      axios
        .get(url)
        .then((res) => {
          console.log(res.data);
          //const datefns = require("date-fns");
          const arr = res.data.daily.time;

          arr.map((date) => {
            this.nameday.push(format(new Date(date), "	EEEE"));
          });

          this.min = res.data.daily.temperature_2m_min;
          this.max = res.data.daily.temperature_2m_max;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    gettemp() {
      this.aa = this.min;
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
    #08244f 0%,
    #134cb5 47.38%,
    #0b42ab 100%
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
.location-drop {
  width: 5%;
  height: 40%;
}
.header {
  display: flex;
  justify-content: space-between;
}
.footer-sum {
  display: flex;
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
.weather-content-img {
  position: absolute;
  top: 0;
  transform: translateX(-37%);
  left: 0;
}
.time {
  display: flex;
  gap: 19%;
  padding-top: 10%;
}
.time p {
  margin: 0;
}
.weather-content-list {
  display: flex;
  margin: 25px 0;
  gap: 18%;
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
</style>
