<template>
  <v-main>
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
          <v-col>
            <v-btn color="success" type="submit">Get Weather</v-btn></v-col
          >
        </v-row>
        <!--  {{ fiveDay }}
        {{ cities }}
        {{ error }} -->
      </v-form>
      <v-data-table :headers="headers" :items="cities" class="elevation-1">
        <template v-slot:[`item.see_more`]="{ item }">
          <v-icon small class="mr-2" @click="fetchFiveDay(item)">
            mdi-chevron-down mdi-24px
          </v-icon>
        </template>
      </v-data-table>
      <v-btn @click="showMin">Show Min</v-btn>
      <v-btn @click="showMax">Show Max</v-btn>
      <v-btn @click="showMean">Show Mean</v-btn>
      <v-btn @click="showMode">Show Mode</v-btn>
      <p>Minimum Temperature: {{ min }}</p>
      <p>Maximum Temperature: {{ max }}</p>
      <p>Mean Temperature: {{ mean }}</p>
      <p>Mode Temperature: {{ mode }}</p>
    </v-container>
    <v-container id="fiveDays">
      <v-data-table
        :headers="fiveDayHeaders"
        :items="fiveDay"
        class="elevation-1"
      >
      </v-data-table>
    </v-container>
  </v-main>
</template>
<script>
export default {
  data() {
    return {
      city: "",
      dialog: false,
      fiveDay: [],
      expanded: [],
      singleExpand: false,
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
        { text: "Clouds", value: "clouds" },
        {
          text: "5-day forecast",
          align: "start",
          sortable: false,
          value: "see_more"
        }
      ],
      fiveDayHeaders: [
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
        { text: "Clouds", value: "clouds" },
        {
          text: "Date and Time",
          align: "start",
          sortable: false,
          value: "time"
        }
      ],
      cities: [],
      fiveDayCities: [],
      min: "",
      max: "",
      mean: "",
      mode: ""
    };
  },

  methods: {
    async fetchApi() {
      await this.$axios
        .$get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${process.env.api}`
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
    async fetchFiveDay(city) {
      this.fiveDay = [];
      console.log("five");
      await this.$axios
        .$get(
          `https://api.openweathermap.org/data/2.5/forecast?q=${city.city}&appid=${process.env.api}`
        )
        .then(res => {
          console.log(res);
          let day = new Date(res.list[0].dt * 1000);
          //   this.fiveDay.push(day);

          let city = res.city.name;
          for (let i = 0; i < res.list.length; i++) {
            let weather = res.list[i].weather[0].description;
            let temp = res.list[i].main.temp;
            let temp_max = res.list[i].main.temp_max;
            let temp_min = res.list[i].main.temp_min;
            let humidity = res.list[i].main.humidity;
            let clouds = res.list[i].clouds.all;
            let time = this.timeConverter(res.list[i].dt);

            this.fiveDay.push({
              city,
              weather,
              temp,
              temp_max,
              temp_min,
              humidity,
              clouds,
              time
            });
          }
          /*    let city = res.name;
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
          }); */
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
    showMode() {
      let cities = [...this.cities];
      let numbers = [];
      let modes = [];
      let count = [];
      let i;
      let number;
      let maxIndex = 0;

      for (let i = 0; i < cities.length; i += 1) {
        numbers.push(cities[i].temp);
      }

      console.log(numbers);

      for (i = 0; i < numbers.length; i += 1) {
        number = numbers[i];
        count[number] = (count[number] || 0) + 1;
        if (count[number] > maxIndex) {
          maxIndex = count[number];
        }
      }

      for (i in count)
        if (count.hasOwnProperty(i)) {
          if (count[i] === maxIndex) {
            modes = Number(i);
          }
        }

      this.mode = modes;
    },
    timeConverter(UNIX_timestamp) {
      let unixDate = new Date(UNIX_timestamp * 1000);
      let months = [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec"
      ];
      let year = unixDate.getFullYear();
      let month = months[unixDate.getMonth()];
      let date = unixDate.getDate();
      let time = `${date} ${month} ${year}`;
      return time;
    }
  }
};
</script>
