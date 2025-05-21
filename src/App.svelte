<script lang="ts">
  import Theme from "../lib/Theme.svelte";
  import { onMount } from "svelte";

  let city = "Delhi"; // bindable
  const apiKey = import.meta.env.VITE_OPENWEATHER_API_KEY;
  let weatherData: any = null;

  async function getWeather(api_link: string) {
    const repsonse = await fetch(api_link);
    const data = await repsonse.json();
    weatherData = data;
    console.log(data);
  }

  onMount(() => {
    const url = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&APPID=${apiKey}`;
    getWeather(url);
  });

  function refreshWeather() {
    const url = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&APPID=${apiKey}`;
    getWeather(url);
  }

  function kelvinToCelsius(k: number) {
    return (k - 273.15).toFixed(1);
  }
</script>

<main class="p-6 max-w-5xl mx-auto">
  <Theme />
  <h1 class="text-3xl font-bold mb-6 text-center">🌤️ Weather Forecast</h1>

  <div class="flex md:flex-row flex-col flex-wrap justify-center mb-10">
    <input
      type="text"
      bind:value={city}
      class="input input-bordered w-full max-w-xs"
      placeholder="Enter city name"
    />
    <button on:click={refreshWeather} class="max-md:btn-block btn btn-primary">
      Get Forecast
    </button>
  </div>

  {#if weatherData}
    <div class="text-center mb-4">
      <h2 class="text-xl font-semibold">
        {weatherData.city.name}, {weatherData.city.country}
      </h2>
      <p class="text-gray-600">
        Lat: {weatherData.city.coord.lat}, Lon: {weatherData.city.coord.lon}
      </p>
    </div>

    <div class="flex flex-wrap justify-center gap-4">
      {#each weatherData.list.slice(0, 8) as item}
        <div
          class={`p-5 rounded-xl md:w-64 w-full shadow-md ${
            Math.abs(new Date(item.dt_txt).getTime() - Date.now()) <
            3 * 60 * 60 * 1000
              ? "bg-primary text-primary-content"
              : "bg-base-200"
          }`}
        >
          <p class="text-sm mb-2">
            {new Date(item.dt_txt).toLocaleString("en-US", {
              day: "numeric",
              month: "long",
              year: "numeric",
              hour: "numeric",
              minute: "2-digit",
              hour12: true,
            })}
          </p>

          <div class="flex items-center justify-between mb-2">
            <img
              src={`https://openweathermap.org/img/wn/${item.weather[0].icon}@2x.png`}
              alt={item.weather[0].description}
              class="w-12 h-12"
            />
            <div>
              <p class="text-lg font-bold">
                {kelvinToCelsius(item.main.temp)}°C
              </p>
              <p class="capitalize text-sm">{item.weather[0].description}</p>
            </div>
          </div>

          <p class="text-sm">💧 Humidity: {item.main.humidity}%</p>
          <p class="text-sm">💨 Wind: {item.wind.speed} m/s</p>
        </div>
      {/each}
    </div>
  {/if}
</main>
<div class="h-20 flex justify-center items-center">
  <p>
    Made with 💙 and ☕ by <a
      href="https://github.com/nabeelv7"
      class="link"
      target="_blank">Nabeel</a
    >
  </p>
</div>
