<template>
	<div class="page">
		<div id="main" :class="isDay ? 'day' : 'night'">
			<h1 class="title text-center m-4">Weather app</h1>
			<form class="search-location m-2 p-5" v-on:submit.prevent="getWeather">
				<input
					type="text"
					class="form-control text-muted form-rounded p-4 shadow-sm"
					placeholder="What City?"
					v-model="citySearch"
					autocomplete="off"
				/>
			</form>
			<p class="text-center my-3" v-if="cityFound">No city found</p>

			<div>
				<div icon="sunny" v-if="clearSky" data-label="Sunny">
					<img src="../src/assets/img/sun.png" alt="" class="img-icons" />
				</div>

				<div icon="snowy" v-if="snowy" data-label="Chilly">
					<img src="../src/assets/img/snow.png" alt="" class="img-icons" />
				</div>

				<div icon="stormy" v-if="stormy" data-label="Soggy">
					<img src="../src/assets/img/light.png" alt="" class="img-icons" />
				</div>

				<div icon="cloudy" v-if="cloudy" data-label="Perfect">
					<img src="../src/assets/img/weather.png" alt="" class="img-icons" />
				</div>

				<div icon="nightmoon" v-if="clearNight" data-label="Cool!">
					<img src="../src/assets/img/night.png" alt="" class="img-icons" />
				</div>
			</div>

			<div class="card-top text-center" style="margin-bottom: 15rem">
				<div class="city-name my-3">
					<img src="../src/assets/img/place.png" alt="" class="img-icons" />
					<p>{{ weather.cityName }}</p>
					<p class="">{{ weather.country }}</p>
				</div>
			</div>

			<div class="card-body">
				<div class="card-mid">
					<div class="row">
						<div class="col-12 text-center temp">
							<span>{{ weather.temperature }}&deg;C</span>
							<p class="my-4">{{ weather.description }}</p>
						</div>
					</div>
					<div class="row">
						<div class="col d-flex justify-content-between px-5 mx-5">
							<p>
								<img src="./assets/down.svg" alt="" />
								{{ weather.lowTemp }}&deg;C
							</p>
							<p>
								<img src="./assets/up.svg" alt="" />
								{{ weather.highTemp }}&deg;C
							</p>
						</div>
					</div>
				</div>
			</div>

			<div id="main" class="card-box">
				<div class="card-box-like">
					<div class="col text-center">
						<p>{{ weather.feelsLike }}&deg;C</p>
						<span>Feels like</span>
					</div>
				</div>

				<div class="card-box-humidity">
					<div class="col text-center">
						<p>{{ weather.humidity }}%</p>
						<span>humidity</span>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			cityFound: false,
			visible: false,
			stormy: false,
			cloudy: false,
			clearSky: false,
			clearNight: false,
			snowy: false,

			isDay: true,
			citySearch: "",
			weather: {
				cityName: "Gwarinpa",
				country: "NG",
				temperature: 12,
				description: "Clouds everywhere",
				lowTemp: "19",
				highTemp: "30",
				feelsLike: "20",
				humidity: "55",
			},
		};
	},
	methods: {
		getWeather: async function() {
			console.log(this.citySearch);
			const key = "40ad7bff961e06f4ff202636ea1fd452";
			const baseURL = `https://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;

			//fetch weather
			try {
				const response = await fetch(baseURL);
				const data = await response.json();
				console.log(data);
				this.citySearch = "";
				this.weather.cityName = data.name;
				this.weather.country = data.sys.country;
				this.weather.temperature = Math.round(data.main.temp);
				this.weather.description = data.weather[0].description;
				this.weather.lowTemp = Math.round(data.main.temp_min);
				this.weather.highTemp = Math.round(data.main.temp_max);
				this.weather.feelsLike = Math.round(data.main.feels_like);
				this.weather.humidity = Math.round(data.main.humidity);

				const timeOfDay = data.weather[0].icon;

				///check for time of day
				if (timeOfDay.includes("n")) {
					this.isDay = false;
				} else {
					this.isDay = true;
				}

				const mainWeather = data.weather[0].main;
				//check weather animations
				if (mainWeather.includes("Clouds")) {
					this.stormy = false;
					this.cloudy = true;
					this.clearSky = false;
					this.clearNight = false;
					this.snowy = false;
				}
				if (mainWeather.includes("Clouds")) {
					this.stormy = false;
					this.cloudy = true;
					this.clearSky = false;
					this.clearNight = false;
					this.snowy = false;
				}
				if (
					mainWeather.includes("Thunderstorm") ||
					mainWeather.includes("Rain")
				) {
					this.stormy = true;
					this.cloudy = false;
					this.clearSky = false;
					this.clearNight = false;
					this.snowy = false;
				}
				if (mainWeather.includes("Clear") && this.isDay) {
					this.stormy = false;
					this.cloudy = false;
					this.clearSky = true;
					this.clearNight = false;
					this.snowy = false;
				}
				if (mainWeather.includes("Clouds") && !this.isDay) {
					this.stormy = false;
					this.cloudy = false;
					this.clearSky = false;
					this.clearNight = true;
					this.snowy = false;
				}
				if (mainWeather.includes("Snow")) {
					this.stormy = false;
					this.cloudy = false;
					this.clearSky = false;
					this.clearNight = false;
					this.snowy = true;
				}

				this.visible = true;
				this.cityFound = false;
			} catch (error) {
				console.log(error);
				this.cityFound = true;
				this.visible = false;
			}
		},
	},
};
</script>

<style scoped>
@import "./assets/custom.css";
@import "./assets/animation.css";
</style>
