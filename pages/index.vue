<template>
  <v-container>
    <h1>Tivix Coding Task</h1>
    <v-form @submit.prevent="fetchApi">
      <v-row>
        <v-col cols="10">
          <v-text-field
            name="city"
            label="Please enter city"
            id="city"
            outlined
            dense
            v-model="city"
          ></v-text-field
        ></v-col>
        <v-col> <v-btn color="success" type="submit">Get Weather</v-btn></v-col>
      </v-row>

      {{ cities }}
      {{ error }}
    </v-form>
    <v-data-table
      :headers="headers"
      :items="cities"
      :sort-by="['temp', 'temp_max', 'temp_min']"
      :sort-desc="[true, false]"
      class="elevation-1"
    ></v-data-table>
    <v-btn @click="showMin">Show Min</v-btn>
    <v-btn @click="showMax">Show Max</v-btn>
    <v-btn @click="showMean">Show Mean</v-btn>
    <p>Minimum Temperature: {{ min }}</p>
    <p>Maximum Temperature: {{ max }}</p>
    <p>Mean Temperature: {{ mean }}</p>
  </v-container>
</template>
<script>
export default {
  data() {
    return {
      city: "",
      apiInfo: "value",
      error: "",
      headers: [
        {
          text: "City",
          align: "start",
          sortable: false,
          value: "city"
        },
        { text: "Weather", value: "weather" },
        { text: "Temperature", value: "temp" },
        { text: "Max Temperature", value: "temp_max" },
        { text: "Min Temperature", value: "temp_min" },
        { text: "Humidity", value: "humidity" },
        { text: "Clouds", value: "clouds" }
      ],
      cities: [],
      min: "",
      max: "",
      mean: ""
    };
  },
  methods: {
    async fetchApi() {
      await this.$axios
        .$get(
          `http://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${process.env.api}`
        )
        .then(res => {
          let city = res.name;
          let weather = res.weather[0].description;
          let temp = res.main.temp;
          let temp_max = res.main.temp_max;
          let temp_min = res.main.temp_min;
          let humidity = res.main.humidity;
          let clouds = res.clouds.all;
          this.cities.push({
            city,
            weather,
            temp,
            temp_max,
            temp_min,
            humidity,
            clouds
          });
        })
        .catch(error => {
          this.error = error;
        });
    },
    showMin() {
      let min = Math.min(...this.cities.map(city => city.temp_min));
      this.min = min;
    },
    showMax() {
      let max = Math.max(...this.cities.map(city => city.temp_max));
      this.max = max;
    },
    showMean() {
      let total = 0;
      let cities = [...this.cities];
      console.log(total);
      //total = total.temp.reduce((a, b) => a + b, 0);
      console.log(total);

      for (let i = 0; i < cities.length; i += 1) {
        total += cities[i].temp;
      }
      let mean = total / cities.length;

      this.mean = mean;
    },

  }
};
</script>
