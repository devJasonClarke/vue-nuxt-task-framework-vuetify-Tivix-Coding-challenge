<template>
  <v-container>
    <h1>Tivix Coding Task</h1>
    <v-form @submit.prevent="fetchApi">
      <v-text-field
        name="name"
        label="label"
        id="id"
        outlined
        v-model="city"
      ></v-text-field>
      {{ posts }}
      {{ cities }}
      {{ error }}
      <v-btn color="success" type="submit">text</v-btn>
    </v-form>
    <v-data-table
      :headers="headers"
      :items="cities"
      :sort-by="['temp', 'temp_max', 'temp_min']"
      :sort-desc="[false, true]"
      class="elevation-1"
    ></v-data-table>
    <v-btn @click="showMin">Show Min</v-btn>
    <p>Minimum Temperature: {{ min }}</p>
    <p>Minimum Temperature: {{ min }}</p>
    <p>Minimum Temperature: {{ min }}</p>
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
          text: "Country",
          align: "start",
          sortable: false,
          value: "country"
        },
        { text: "Weather", value: "weather" },
        { text: "Temperature", value: "temp" },
        { text: "Max Temperature", value: "temp_max" },
        { text: "Min Temperature", value: "temp_min" },
        { text: "Humidity", value: "humidity" },
        { text: "Clouds", value: "clouds" }
      ],
      cities: [],
      min: []
    };
  },
  methods: {
    async fetchApi() {
      await this.$axios
        .$get(
          `http://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${process.env.api}`
        )
        .then(res => {
          let country = res.name;
          let weather = res.weather[0].description;
          let temp = res.main.temp;
          let temp_max = res.main.temp_max;
          let temp_min = res.main.temp_min;
          let humidity = res.main.humidity;
          let clouds = res.clouds.all;
          this.cities.push({
            country,
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
      let min = Math.min(...this.cities.map(city => city.temp));
      this.min.push(min);
    }
  }
};
</script>
