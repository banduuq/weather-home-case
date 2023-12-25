<template>
  <div id="ban">
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
    </div>
     <div class="weather-wrap"  v-if="weatherData.length > 0">
      <div v-for="item in weatherData " :key="item.id">
        <div class="location-box">
          <div class="location">{{ item.name }}, {{ item.sys.country }}</div>
          <div class="date">{{ dateBuilder().Date }}</div>
        </div>
        <div class="weather-box">
          <div class="temp">{{ Math.round(item.main.temp) }}°c</div>
          <div class="weather">{{ $t(item.weather[0].main) }}</div>
          <div class="weather">{{Math.round(item.main.temp_min) }}°c /
            {{Math.round(item.main.temp_max) }}°c</div>
        </div>
      </div>
     </div>
     <div class="lang">
        <span  @click="changeLanguage('en')" :class="{ 'selected-language': currentLanguage === 'en' }">En</span>
        <span @click="changeLanguage('tr')" :class="{ 'selected-language': currentLanguage === 'tr' }">Tr</span>
        <span @click="changeLanguage('som')" :class="{ 'selected-language': currentLanguage === 'som' }">Som</span>
      </div>
     <div v-if="dailyForecasts.length > 0">
       <table>
         <tr>
           <th>{{$t("Time")}}</th>
           <th>{{$t("Temprature")}}</th>
           <th>{{$t("feelslike")}}</th>
           <th>{{$t("humidity")}}</th>
           <th>{{$t("winddirection")}}</th>
           <th>{{$t("windspeed")}}</th>
           <th>{{$t("windmaxspeed")}}</th>
         </tr>
           <tr  v-for="item in dailyForecasts " :key="item.id">
             <td>{{ dateBuilder().currenDay }} <br> {{item.dt_txt.split(" ")[1] }}</td>
              <td>{{ Math.round(item.main.temp)  }}°C</td>
              <td>{{ Math.round(item.main.feels_like )}}°C</td>
              <td>{{ Math.round(item.main.humidity) }}</td>
              <td>{{ Math.round(item.wind.deg) }}</td>
              <td>{{ Math.round(item.wind.speed) }}</td>
              <td>{{ Math.round (item.wind.gust) }}</td>
           </tr>
       </table>
     </div>
    </main>
  </div>
   <router-view></router-view>
</template>
<script>
export default {
  name: 'home',
  data() {
    return {
      key: '149161a287a4f4a7203c287e84f3fedb',
      baseUrl: 'https://api.openweathermap.org/data/2.5/',
      defaultCities: ['ankara', 'istanbul'],
      query: '',
      weatherData: [],
      dailyForecasts: [],
      currentLanguage: 'en',
    };
  },
  methods: {
    fetchWeather(e) {
      if (e.key === 'Enter') {
        this.weatherData = []; 
        this.dailyForecasts = [];
       this.fetchWeatherByCity(this.query);
       
      }
    },
async fetchWeatherByCity(city) {
      try {
        const response = await fetch(`${this.baseUrl}weather?q=${city}&APPID=${this.key}&units=metric`);
        if (!response.ok) {
          throw new Error(`Weather data not available for ${city}`);
        }
        const result = await response.json();
        this.weatherData.push(result);
        await this.fetchDailyForecasts(city);
      } catch (error) {
        console.error(error.message);
      }
    },

    dateBuilder() {
      let d = new Date();
      let months = [
      this.$t('January'),
      this.$t('February'),
      this.$t('March'),
      this.$t('April'),
      this.$t('May'),
      this.$t('June'),
      this.$t('July'),
      this.$t('August'),
      this.$t('September'),
      this.$t('October'),
      this.$t('November'),
      this.$t('December'),
      ];
      let days = [
      this.$t('Sunday'),
      this.$t('Monday'),
      this.$t('Tuesday'),
      this.$t('Wednesday'),
      this.$t('Thursday'),
      this.$t('Friday'),
      this.$t('Saturday'),
      ];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return {
       Date: `${day} ${date} ${month} ${year}`,
       currenDay: day
      }
     
    },

    async fetchDailyForecasts(city) {
      try {
        const response = await fetch(`${this.baseUrl}forecast?q=${city},tr&appid=${this.key}&units=metric`);
        if (!response.ok) {
          throw new Error(`Weather data not available for ${city}`);
        }
        const results = await response.json();
        const currentDate = new Date().toISOString().split('T')[0];
        this.dailyForecasts.push(...results.list.filter(item => item.dt_txt.includes(currentDate)));
        console.log("dailyForecasts", this.dailyForecasts);
      } catch (err) {
        console.log(err.message);
      }
    },

    changeLanguage(lang) {
      this.$i18n.locale = lang;
     this.currentLanguage = lang;
    },
  },  
 
 async mounted() {
  try {
   await Promise.all (this.defaultCities.map(city => this.fetchWeatherByCity(city)))
    console.log("All data is fetched");
    console.log("Daily Forecasts Length:", this.dailyForecasts.length);
  } catch (error) {
    console.error("Error fetching weather data:", error.message);
  }
},
}
</script>


<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#ban {
  background-image: url('../assets/warm-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
  
}
.weather-wrap{
  display: flex;
  flex-direction: row;
  justify-content: space-around;
}

.lang {
  display: flex;
  justify-content: flex-end;
  position: absolute;
  top: 90px;
  right: 20px;
}

.lang span {
  padding: 10px;
  margin-top: 10;
  color: #FFF;
  font-size: 20px;
}
.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  
  color: #313131;
  font-size: 20px;

  appearance: none;
  border:none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
  padding: 30px;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px px;
  color: #FFF;
  font-size: 90px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color:rgba(185, 172, 172, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather {
  color: #FFF;
  font-size: 20px;
  font-weight: 400;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  margin: 30px 0px;
}
table, th, td {
  border-collapse: collapse;
}
th, td {
  padding-bottom: 2%;
  padding-top: 2%;
  padding-left:2%; 
}
tr{
  background-color: #323C50;
}
th {
  background-color: #1F2739;
  color: #FFF; 
}
td{
  color: #FFF; 
}
tr:hover {
   background-color: #464A52;
-webkit-box-shadow: 0 6px 6px -6px #0E1119;
     -moz-box-shadow: 0 6px 6px -6px #0E1119;
          box-shadow: 0 6px 6px -6px #0E1119;
}
td:hover {
  background-color: #48d8f2;
  color: #403E10;
  font-weight: bold;
  
  box-shadow: #7F7C21 -1px 1px, #7F7C21 -2px 2px, #7F7C21 -3px 3px, #7F7C21 -4px 4px, #7F7C21 -5px 5px, #7F7C21 -6px 6px;
  transform: translate3d(6px, -6px, 0);
  
  transition-delay: 0s;
    transition-duration: 0.4s;
    transition-property: all;
  transition-timing-function: line;
}
table{
  text-align: left;
    overflow: hidden;
    width: 80%;
    margin: 0 auto;
  display: table;
  padding: 0 0 4em 0;
}
.lang span.selected-language {
  color: #36aadc; 
  font-weight: bold;
}
</style>