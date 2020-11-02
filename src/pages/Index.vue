<template>
  <q-page class="flex column" :class="bgClass">
    <!--============ Search section Start ============-->
    <div class="col q-pt-lg q-px-md">
      <q-input
        standout
        v-model="search"
        @keyup.enter="getWeatherBySearch"
        placeholder="Search"
        dark
        borderless
        maxlength="20"
      >
        <template v-slot:before>
          <q-icon @click="getLocation" name="my_location" dense />
        </template>

        <template v-slot:append>
          <q-btn
            @click="getWeatherBySearch"
            round
            dense
            flat
            margin-top
            icon="search"
          />
          <q-icon
            v-if="search !== ''"
            name="close"
            @click="search = ''"
            class="cursor-pointer"
          />
        </template>
      </q-input>
    </div>

    <!--============ Search section End ============-->
    <!--Else statement-->
    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{ weatherData.name }}
        </div>
        <div class="text-h6 text-weight-light q-pt-md">
          {{ weatherData.weather[0].main }}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp) }}</span>
          <span class="text-h4 relative-position degree">&deg;</span>
        </div>
      </div>

      <div class="col text-center">
        <img
          :src="
            `http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`
          "
        />
      </div>
    </template>
    <template v-else="weatherData">
      <div class="class col column text-center text-white">
        <div class="col text-h2 text-weight-thin">Quasar<br />Weather</div>
        <q-btn @click="getLocation" class="col" flat>
          <q-icon left size="4em" flex-center name="my_location" />
          <div>Find My Location</div>
        </q-btn>
      </div>
    </template>
    <div class="col skyline"></div>

    <!-- 
            "../assets/buildings.png"  ../ -> one folder outside
            .../ -> src folder outside
        -->
  </q-page>
</template>

<script>
export default {
  name: "PageIndex",
  data() {
    return {
      search: "",
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: "https://api.openweathermap.org/data/2.5/weather",
      apiKey: "4e5b5385f6fe76c0440b1f4d16e6b315",
    };
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith("n")) {
          return 'bg-night'
        } else {
          return 'bg-day'
        }
      }
    },
  },
  methods: {
    getLocation() {
        this.$q.loading.show()
      navigator.geolocation.getCurrentPosition((position) => {
        console.log(position);
        this.lat = position.coords.latitude;
        this.lon = position.coords.longitude;
        this.getWeatherByCoords();
      });
    },
    getWeatherByCoords() {
        this.$q.loading.show()
      this.$axios(
        `${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`
      ).then((response) => {
        this.weatherData = response.data;
    
          this.$q.loading.hide()
      });
    },
    getWeatherBySearch() {
        this.$q.loading.show()
      console.log("getWeatherBySearch");
      this.$axios(
        `${this.apiUrl}?q=${this.search}&appid=${this.apiKey}&units=metric`
      ).then((response) => {
        this.weatherData = response.data;
          this.$q.loading.hide()
      });
    },
  },
};
</script>
<style lang="sass">
.q-page
  background: linear-gradient(to left, #8360c3, #2ebf91);
  &.bg-day
    background: linear-gradient(to top, #2193b0, #6dd5ed);
  &.bg-night
    background: linear-gradient(to top, #000000, #434343);
.degree
  top: -44px;

.skyline
  flex: 0 0 23em;
  background: url(../assets/buildings.png);
  background-size: contain;
  background-position: bottom center;
</style>
