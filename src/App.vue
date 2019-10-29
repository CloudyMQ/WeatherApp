<template>
  <div id = "app" v-if="display">
    <b-container class="widget">
      <h1 id="location">Weather in {{location}}</h1>
      <b-row >
        <b-col align-self="start" cols = "3" id="weather1" class="weather">
          <img class="icon" :src="weather1.icon" v-if="display">
          <span class="temperature" v-if="display">{{weather1.averageTemp}}°</span>
          <span id="date" v-if="display">{{weather1.dt.toString().slice(4,10)}}</span> 
        </b-col>
        <b-col align-self="center" cols = "3" id="weather2" class="weather">
          <img class="icon" :src="weather2.icon" v-if="display">
          <span class="temperature" v-if="display">{{weather2.averageTemp}}°</span>
          <span id="date" v-if="display">{{weather2.dt.toString().slice(4,10)}}</span>
        </b-col>
        <b-col align-self="end" cols = "3" id="weather3" class="weather">
          <img class="icon" :src="weather3.icon" v-if="display">  
          <span class="temperature" v-if="display">{{weather3.averageTemp}}°</span>
          <span id="date" v-if="display">{{weather3.dt.toString().slice(4,10)}}</span>
        </b-col>
  </b-row>
      </b-container>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data () {
    return {
      weather1: {
        id: 0,
        averageTemp: 0,
        dt:'',
        icon: '',
      },
      weather2: {
        id: 1,
        averageTemp: 0,
        dt:'',
        icon: '',
      },
      weather3: {
        id: 2,
        averageTemp: 0,
        dt:'',
        icon: '',
      },
      location: '',
      temperatures: [],
      display: false
    }
  },
  methods: {
    getWeather() {
      var date = new Date();
      var sumToTemp = [0,0,0];
      var countTemp = [0,0,0];
      var minTimeDifference = [0,0,0];
      var tmpDate = 0;
      let url ="http://api.openweathermap.org/data/2.5/forecast?q=Ростов-на-Дону&units=metric&APPID=4e6a222e1435829f560d4943f75a8716";
      axios
      .get(url)
      .then(response => {
          console.log(response.data);
          if (!response.data) return
          this.location = response.data.city.name;
          this.temperatures = response.data.list;
          this.weather1.dt = new Date();
          this.weather1.dt.setDate(this.weather1.dt.getDate() + this.weather1.id);
          this.weather2.dt = new Date();
          this.weather2.dt.setDate(this.weather2.dt.getDate() + this.weather2.id);
          this.weather3.dt = new Date();
          this.weather3.dt.setDate(this.weather3.dt.getDate() + this.weather3.id);
          for (let i = 0; i < this.temperatures.length-1; i++) {
            tmpDate = new Date(response.data.list[i].dt*1000);
            tmpDate.setMinutes(tmpDate.getMinutes() + tmpDate.getTimezoneOffset());
            if(this.weather1.dt.getDate() == tmpDate.getDate()){
              sumToTemp[this.weather1.id] = sumToTemp[this.weather1.id] + response.data.list[i].main.temp;
              countTemp[this.weather1.id] ++;
              if(Math.abs(tmpDate.getHours() - this.weather1.dt.getHours()) < minTimeDifference[this.weather1.id]){
                minTimeDifference[this.weather1.id] = i;
              }
            }
            if(this.weather2.dt.getDate() == tmpDate.getDate()){
              sumToTemp[this.weather2.id] = sumToTemp[this.weather2.id] + response.data.list[i].main.temp;
              countTemp[this.weather2.id] ++;
              if(minTimeDifference[this.weather2.id] == 0){
                minTimeDifference[this.weather2.id] = i;
              }
            }
            if(this.weather3.dt.getDate() == tmpDate.getDate()){
              sumToTemp[this.weather3.id] = sumToTemp[this.weather2.id] + response.data.list[i].main.temp;
              countTemp[this.weather3.id] ++;
              if(minTimeDifference[this.weather3.id] == 0){
                minTimeDifference[this.weather3.id] = i;
              }
            }
          }
          this.weather1.averageTemp = Math.round(sumToTemp[this.weather1.id] / countTemp[this.weather1.id]);
          this.weather2.averageTemp = Math.round(sumToTemp[this.weather2.id] / countTemp[this.weather2.id]);
          this.weather3.averageTemp = Math.round(sumToTemp[this.weather3.id] / countTemp[this.weather3.id]);
          this.weather1.icon = "static/"+response.data.list[minTimeDifference[this.weather1.id]].weather[0].icon.slice(0,2) + ".svg"
          this.weather2.icon = "static/"+response.data.list[minTimeDifference[this.weather2.id]].weather[0].icon.slice(0,2) + ".svg"
          this.weather3.icon = "static/"+response.data.list[minTimeDifference[this.weather3.id]].weather[0].icon.slice(0,2) + ".svg"
          this.display = true
        })
        .catch(error => {
          console.log(error)
        });
    },
  },
  beforeMount() { 
      this.getWeather();

  },
}
</script>

<style>
.widget {
  width: 40%;
  background-color: #702963;
  border-radius: 20px;
  margin: 5%;
}
.weather {
  width: 50%;
  background-color: rgba(112, 104, 104, 0.5);
  border-radius: 20px;
  margin: 4%;
  color:white;
  text-align: center;
  font-size: 120%;
  text-shadow: 1px 0 1px #000, 
  0 1px 1px #000, 
  -1px 0 1px #000, 
  0 -1px 1px #000;
}
.icon {
  width: 100%;
  height: 100%;
}
#location {
  padding-top: 1%;
  text-align: center;
  color: white;
  text-shadow: 1px 0 1px #000, 
  0 1px 1px #000, 
  -1px 0 1px #000, 
  0 -1px 1px #000;
}
</style>
