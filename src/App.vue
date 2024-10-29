<template>
  <v-app class="body">
    <v-container>
      <v-row justify="center">
        <v-col cols="12" md="6">
          <v-card class="pa-6 elevation-3">
            <v-card-title class="justify-center">
              <h2 class="text-center primary--text font-weight-bold">Weather App</h2>
            </v-card-title>
            <v-card-text>
              <v-text-field
                v-model="city"
                label="Enter City Name"
                outlined
                color="primary"
                clearable
                prepend-inner-icon="mdi-city"
                class="mb-4"
                @keyup.enter="addCity"
              ></v-text-field>
              <v-btn color="primary" class="mb-4" @click="addCity" block>
                <v-icon left>mdi-cloud-search</v-icon> Add City
              </v-btn>

              <v-alert v-if="error" type="error" class="mb-4">{{ error }}</v-alert>

              <!-- Loop through each city's weather data -->
              <v-row v-if="weatherList.length > 0">
                <v-col
                  v-for="(weather, index) in weatherList"
                  :key="index"
                  cols="12"
                  class="mb-4"
                >
                  <v-card class="weather-card pa-3" outlined>
                    <!-- Remove City Button inside the card -->
                    <v-btn
                      icon
                      color="red"
                      @click="removeCity(index)"
                      class="remove-city-btn"
                      small
                    >
                      <v-icon>mdi-close</v-icon>
                    </v-btn>

                    <!-- Centering the City Title -->
                    <v-card-title class="justify-center text-center">
                      <v-icon left class="mr-2">mdi-city</v-icon>
                      <span class="text-h5 font-weight-bold">{{ weather.name }}</span>
                    </v-card-title>
                    <v-card-subtitle class="text-h6 mb-2 text-center">
                      {{ weather.weather[0].description }}
                    </v-card-subtitle>
                    <v-divider></v-divider>

                    <!-- Weather information in rows -->
                    <v-card-text>
                      <v-row class="weather-info-row">
                        <v-col cols="6">
                          <v-icon size="40" color="blue">mdi-thermometer</v-icon>
                          <p><strong>Temperature:</strong> {{ weather.main.temp }}°C</p>
                        </v-col>
                        <v-col cols="6">
                          <v-icon size="40" color="orange">mdi-weather-sunset-up</v-icon>
                          <p><strong>Sunrise:</strong> {{ formatTime(weather.sys.sunrise) }}</p>
                        </v-col>
                      </v-row>

                      <v-row class="weather-info-row">
                        <v-col cols="6">
                          <v-icon size="40" color="purple">mdi-weather-sunset-down</v-icon>
                          <p><strong>Sunset:</strong> {{ formatTime(weather.sys.sunset) }}</p>
                        </v-col>
                        <v-col cols="6">
                          <v-icon size="40" color="cyan">mdi-wind-turbine</v-icon>
                          <p><strong>Wind Speed:</strong> {{ weather.wind.speed }} m/s</p>
                        </v-col>
                      </v-row>

                      <v-row class="weather-info-row">
                        <v-col cols="6">
                          <v-icon size="40" color="green">mdi-water-percent</v-icon>
                          <p><strong>Humidity:</strong> {{ weather.main.humidity }}%</p>
                        </v-col>
                        <v-col cols="6">
                          <v-icon size="40" color="red">mdi-gauge</v-icon>
                          <p><strong>Pressure:</strong> {{ weather.main.pressure }} hPa</p>
                        </v-col>
                      </v-row>
                    </v-card-text>
                  </v-card>
                </v-col>
              </v-row>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      city: "",
      weatherList: [],
      error: "",
    };
  },
  methods: {
    async addCity() {
      if (this.city.trim() === "") {
        this.error = "Please enter a city name.";
        return;
      }
      try {
        const apiKey = "036ae40c1b86ad2be703acb87b3186fc"; // Replace with your OpenWeatherMap API key
        const response = await fetch(
`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${apiKey}`
        );
        const data = await response.json();
        if (data.cod === 200) {
          this.weatherList.unshift(data); // Use unshift to add the city to the top
          this.error = "";
          this.city = "";
        } else {
          this.error = "City not found.";
        }
      } catch (error) {
        this.error = "Unable to fetch weather data.";
      }
    },
    removeCity(index) {
      this.weatherList.splice(index, 1);
    },
    formatTime(timestamp) {
      const date = new Date(timestamp * 1000);
      return date.toLocaleTimeString();
    },
  },
};
</script>


<style>
.body {
  font-family: "Roboto", sans-serif;
  background-color: #BBDEFB;
}

.weather-card {
  background-color: #E3F2FD;
  border-radius: 15px;
  position: relative; /* Ensure relative positioning for the button */
}

.v-btn {
  font-weight: bold;
}

.v-text-field {
  border-radius: 5px;
}

h2 {
  font-family: "Roboto", sans-serif;
}

.weather-info-row {
  margin-bottom: 16px;
}

.text-h5 {
  font-size: 1.25rem;
}

.font-weight-bold {
  font-weight: bold;
}

/* Remove City Button Styling */
.remove-city-btn {
  position: absolute;
  top: -10px; /* Positioning it at the top of the card */
  right: -10px; /* Positioning it at the right of the card */
  background-color: white;
  border-radius: 50%;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.2);
  width: 30px; /* Set a fixed width */
  height: 30px; /* Set a fixed height */
  min-width: 0; /* Prevents minimum width */
  padding: 0; /* Remove padding */
}

.remove-city-btn .v-icon {
  font-size: 18px; /* Smaller icon size */
}
</style>
