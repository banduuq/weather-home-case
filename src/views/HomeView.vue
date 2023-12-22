<template>
  <div id="ban" >
    <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
    </div>

    <main>
      <div class="weather-wrap" v-for="(weather, index) in filteredWeatherData" :key="index">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>
        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
          <div class="weather">{{Math.round(weather.main.temp_min) }}°c /
            {{Math.round(weather.main.temp_max) }}°c</div>
          <div class="weather">{{ weather.weather[0].description }}</div>
        </div>
      </div>

     <div >
       <ul v-if="dailyForecasts.length > 0">
        <li v-for="item in dailyForecasts " v-bind:key="item.id" >
          <p>Temperature: {{ item.temp }}°C</p>
          <p>Description: {{ item.weather[0].description }}</p> 
        </li>
       </ul>
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
      forcast: '95f8c3ac3569be70fd2b191277ef86e6',
      weatherData: [],
      dailyForecasts: [],
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
        const response = await fetch(`${this.baseUrl}weather?q=${city}&APPID=${this.key}&units=metric&lang=tr`);
        if (!response.ok) {
          throw new Error(`Weather data not available for ${city}`);
        }
        const result = await response.json();
        this.weatherData.push(result);

        console.log("city", city);
        await this.fetchDailyForecasts(city);
      } catch (error) {
        console.error(error.message);
      }
    },

    dateBuilder() {
      let d = new Date();
      let months = [
        'January',
        'February',
        'March',
        'April',
        'May',
        'June',
        'July',
        'August',
        'September',
        'October',
        'November',
        'December',
      ];
      let days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },

    async fetchDailyForecasts(city) {
      try {
        const response = await fetch(`${this.baseUrl}forecast?q=${city},tr&appid=${this.key}`);
        if (!response.ok) {
          throw new Error(`Weather data not available for ${city}`);
        }
        const results = await response.json();
        const currentDate = new Date().toISOString().split('T')[0];
        console.log("currentDate",currentDate);
        console.log("newDate",new Date());
        this.dailyForecasts = results.list.filter(item => item.dt_txt.includes(currentDate));
        console.log("dailyForecasts", this.dailyForecasts);
      } catch (err) {
        console.log(err.message);
      }
    },
  },  
  mounted() {
    for (const city of this.defaultCities) {
      this.fetchWeatherByCity(city);
    }
  },
    
  computed: {
  filteredWeatherData() {
    return this.weatherData.filter((weather) => Object.keys(weather).length > 0);
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
#ban {
  background-image: url('../assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#ban.warm {
  background-image: url('../assets/warm-bg.jpg');
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
  display: flex;
  flex-direction: row;
  justify-content: space-around;
}

.search-box {
  width: 100%;
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
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color:rgba(255, 255, 255, 0.25);
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
</style>