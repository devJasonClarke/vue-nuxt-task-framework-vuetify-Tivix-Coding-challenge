<template>
  <v-main class="main-padding bg">
    <v-container>
      <h1 class="mb-6">Tivix Coding Task | Weather App</h1>

      <v-form @submit.prevent="verifyCity" class="pb-6">
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
            <v-btn elevation="0" color="success" type="submit"
              >Get Weather</v-btn
            ></v-col
          >
        </v-row>
      </v-form>
      <v-data-table :headers="headers" :items="cities" class="elevation-1">
        <template v-slot:[`item.see_more`]="{ item }">
          <v-icon small class="mr-2" @click="fetchFiveDay(item)">
            mdi-chevron-down mdi-24px
          </v-icon>
        </template>
      </v-data-table>
      <v-row class="py-12">
        <v-col cols="12" sm="3">
          <v-btn elevation="0" @click="showMin('cities')"
            >Show Min</v-btn
          ></v-col
        >
        <v-col>
          <p>
            Minimum Temperature: {{ min }}
            <span v-if="cities.length !== 0">°C</span>
          </p></v-col
        >
        <v-col cols="12" sm="3">
          <v-btn elevation="0" @click="showMax('cities')"
            >Show Max</v-btn
          ></v-col
        >

        <v-col cols="12" sm="3">
          <p>
            Maximum Temperature: {{ max }}
            <span v-if="cities.length !== 0">°C</span>
          </p></v-col
        >
        <v-col cols="12" sm="3">
          <v-btn elevation="0" @click="showMean('cities')"
            >Show Mean</v-btn
          ></v-col
        >
        <v-col>
          <p>
            Mean Temperature: {{ mean }}
            <span v-if="cities.length !== 0">°C</span>
          </p></v-col
        >
        <v-col cols="12" sm="3">
          <v-btn elevation="0" @click="showMode('cities')"
            >Show Mode</v-btn
          ></v-col
        >

        <v-col>
          <p>
            Mode Temperature: {{ mode }}
            <span v-if="cities.length !== 0">°C</span>
          </p></v-col
        >
      </v-row>
    </v-container>
    <v-divider class="mb-6"></v-divider>
    <v-container id="results">
      <h2 class="mb-6">Five day forecast</h2>
      <v-data-table
        :headers="fiveDayHeaders"
        :items="fiveDay"
        class="elevation-1"
      >
      </v-data-table>
      <v-row class="py-12">
        <v-col cols="12" sm="3">
          <v-btn elevation="0" @click="showMin('fiveDays')"
            >Show Min</v-btn
          ></v-col
        >
        <v-col>
          <p>
            Minimum Temperature (5 days): {{ fiveDayMin }}
            <span v-if="fiveDay.length !== 0">°C</span>
          </p></v-col
        >
        <v-col cols="12" sm="3">
          <v-btn elevation="0" @click="showMax('fiveDays')"
            >Show Max</v-btn
          ></v-col
        >

        <v-col cols="12" sm="3">
          <p>
            Maximum Temperature (5 days): {{ fiveDayMax }}
            <span v-if="fiveDay.length !== 0">°C</span>
          </p></v-col
        >
        <v-col cols="12" sm="3">
          <v-btn elevation="0" @click="showMean('fiveDays')"
            >Show Mean</v-btn
          ></v-col
        >
        <v-col>
          <p>
            Mean Temperature (5 days): {{ fiveDayMean }}
            <span v-if="fiveDay.length !== 0">°C</span>
          </p></v-col
        >
        <v-col cols="12" sm="3">
          <v-btn elevation="0" @click="showMode('fiveDays')"
            >Show Mode</v-btn
          ></v-col
        >

        <v-col>
          <p>
            Mode Temperature (5 days): {{ fiveDayMode }}
            <span v-if="fiveDay.length !== 0">°C</span>
          </p></v-col
        >
      </v-row>
    </v-container>
    <v-snackbar v-model="snackbar">
      {{ error }}

      <template v-slot:action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
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
        { text: "Temperature °C", value: "temp" },
        { text: "Max Temperature °C", value: "temp_max" },
        { text: "Min Temperature °C", value: "temp_min" },
        /*         { text: "Humidity", value: "humidity" },
        { text: "Clouds", value: "clouds" }, */
        {
          text: "Get 5-day forecast",
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
        { text: "Temperature °C", value: "temp" },
        { text: "Max Temperature °C", value: "temp_max" },
        { text: "Min Temperature °C", value: "temp_min" },
        /*      { text: "Humidity", value: "humidity" },
        { text: "Clouds", value: "clouds" }, */
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
      mode: "",
      fiveDayMin: "",
      fiveDayMax: "",
      fiveDayMean: "",
      fiveDayMode: "",
      snackbar: false,
      error: ""
    };
  },

  methods: {
    async fetchApi() {
      await this.$axios
        .$get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${process.env.api}`
        )
        .then(res => {
          this.city = "";
          let city = res.name;
          let weather = res.weather[0].description;
          let temp = res.main.temp;
          let temp_max = res.main.temp_max;
          let temp_min = res.main.temp_min;
          /*      let humidity = res.main.humidity;
          let clouds = res.clouds.all; */
          this.cities.push({
            city,
            weather,
            temp,
            temp_max,
            temp_min
          });
        })
        .catch(() => {
          this.error =
            "Something went wrong. Please check you spelling or the internet.";
          this.snackbar = true;
        });
    },
    async fetchFiveDay(city) {
      this.fiveDay = [];
      console.log("five");
      await this.$axios
        .$get(
          `https://api.openweathermap.org/data/2.5/forecast?q=${city.city}&units=metric&appid=${process.env.api}`
        )
        .then(res => {
          this.$vuetify.goTo("#results");
          console.log(res);
          let day = new Date(res.list[0].dt * 1000);
          //   this.fiveDay.push(day);

          let city = res.city.name;
          for (let i = 0; i < res.list.length; i++) {
            let weather = res.list[i].weather[0].description;
            let temp = res.list[i].main.temp;
            let temp_max = res.list[i].main.temp_max;
            let temp_min = res.list[i].main.temp_min;
            /*      let humidity = res.list[i].main.humidity;
            let clouds = res.list[i].clouds.all; */
            let time = this.timeConverter(res.list[i].dt);

            this.fiveDay.push({
              city,
              weather,
              temp,
              temp_max,
              temp_min,
              time
            });
          }
        })
        .catch(error => {
          this.snackbar = true;
          this.error = error;
        });
    },
    verifyCity() {
      if (this.city !== "") {
        this.fetchApi();
      } else {
        this.snackbar = true;
        this.error = "City is required!";
      }
    },

    showMin(data) {
      if (data == "cities") {
        let min = Math.min(...this.cities.map(city => city.temp_min));
        this.min = min;
      } else {
        let min = Math.min(...this.fiveDay.map(city => city.temp_min));
        this.fiveDayMin = min;
      }
    },
    showMax(data) {
      if (data == "cities") {
        let max = Math.max(...this.cities.map(city => city.temp_max));
        this.max = max;
      } else {
        let max = Math.max(...this.fiveDay.map(city => city.temp_max));
        this.fiveDayMax = max;
      }
    },
    showMean(data) {
      let total = 0;
      let cities;

      if (data == "cities") {
        cities = [...this.cities];
      } else {
        cities = [...this.fiveDay];
      }

      console.log(total);
      //total = total.temp.reduce((a, b) => a + b, 0);
      console.log(total);

      for (let i = 0; i < cities.length; i += 1) {
        total += cities[i].temp;
      }
      let mean = total / cities.length;

      if (data == "cities") {
        this.mean = mean;
      } else {
        this.fiveDayMean = mean;
      }
    },
    showMode(data) {
      let cities;
      let numbers = [];
      let modes = [];
      let count = [];
      let i;
      let number;
      let maxIndex = 0;

      if (data == "cities") {
        cities = [...this.cities];
      } else {
        cities = [...this.fiveDay];
      }

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

      if (data == "cities") {
        this.mode = modes;
      } else {
        this.fiveDayMode = modes;
      }
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
      var hour = unixDate.getHours();
      var minutes = "0" + unixDate.getMinutes();
      let time = `${date} ${month} ${year} - ${hour}:${minutes}`;
      return time;
    }
  }
};
</script>
<style scoped>
.main-padding {
  padding: 30px 5px;
}
</style>
