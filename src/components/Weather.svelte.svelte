<!-- Weather.svelte -->
<script lang="ts">
  import { onMount } from 'svelte';
  import { writable } from 'svelte/store';

  const iconBaseUrl = 'https://openweathermap.org/img/wn/';
  const key = '82005d27a116c2880c8f0fcb866998a0'; // Your API key

  interface WeatherData {
    temperature: { value: number; unit: 'celsius' | 'fahrenheit' };
    description: string;
    iconId: string;
    city: string;
    country: string;
    windSpeed: number;
    humidity: number;
    uvIndex: number;
  }

  const weather = writable<WeatherData>({
    temperature: { value: 0, unit: 'celsius' },
    description: '',
    iconId: 'unknown',
    city: '',
    country: '',
    windSpeed: 0,
    humidity: 0,
    uvIndex: 0
  });

  async function fetchWeatherByCoordinates(latitude: number, longitude: number) {
    const api = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=${key}&units=metric`;
    try {
      const response = await fetch(api);
      const data = await response.json();
      updateWeather(data);
    } catch (error) {
      console.error('Error fetching weather data:', error);
    }
  }

  async function fetchWeatherByCity(city: string) {
    const api = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${key}&units=metric`;
    try {
      const response = await fetch(api);
      const data = await response.json();
      updateWeather(data);
    } catch (error) {
      console.error('Error fetching weather data:', error);
    }
  }

  async function fetchWeatherByPinCode(pincode: string) {
    const api = `https://api.openweathermap.org/data/2.5/weather?zip=${pincode},in&appid=${key}&units=metric`;
    try {
      const response = await fetch(api);
      const data = await response.json();
      updateWeather(data);
    } catch (error) {
      console.error('Error fetching weather data:', error);
    }
  }

  function updateWeather(data: any) {
    if (data.cod === '404') {
      console.error('City not found:', data.message);
      return;
    }
    weather.set({
      temperature: { value: data.main.temp, unit: 'celsius' },
      description: data.weather[0].description,
      iconId: data.weather[0].icon,
      city: data.name,
      country: data.sys.country,
      windSpeed: data.wind.speed,
      humidity: data.main.humidity,
      uvIndex: 5 // Dummy value for demonstration
    });
  }

  onMount(async () => {
    if ('geolocation' in navigator) {
      navigator.geolocation.getCurrentPosition(
        position => {
          const { latitude, longitude } = position.coords;
          fetchWeatherByCoordinates(latitude, longitude);
        },
        error => console.error('Error getting location:', error)
      );
    } else {
      console.error("Browser doesn't support Geolocation");
    }
  });

  function handleSearch(event: Event) {
    event.preventDefault();
    const formData = new FormData(event.target as HTMLFormElement);
    const searchQuery = formData.get('search') as string;
    if (searchQuery) {
      // Check if the entered value is a valid Indian PIN code format (6 digits)
      const isIndianPINCode = /^[1-9][0-9]{5}$/.test(searchQuery);
      if (isIndianPINCode) {
        fetchWeatherByPinCode(searchQuery);
      } else {
        fetchWeatherByCity(searchQuery);
      }
    }
  }

  function celsiusToFahrenheit(celsius: number) {
    return Math.round(celsius * 9 / 5 + 32);
  }
</script>

<main class="min-h-screen bg-cover bg-center bg-blue-200 flex items-center justify-center">
  <div class="bg-white shadow-md rounded-lg overflow-hidden w-full max-w-md">
    <form on:submit={handleSearch} class="px-6 py-4">
      <input type="text" name="search" placeholder="Enter city name or Indian PIN code" class="w-full py-2 px-4 border border-gray-300 rounded-md focus:outline-none focus:ring focus:ring-blue-400" />
      <button type="submit" class="mt-4 w-full bg-blue-500 text-white py-2 px-4 rounded-md hover:bg-blue-600 focus:outline-none focus:ring focus:ring-blue-400">Search</button>
    </form>
    <div class="p-6">
      {#if $weather.city}
        <div class="text-center">
          <img src={`${iconBaseUrl}${$weather.iconId}@2x.png`} alt="Weather Icon" class="mx-auto mb-4">
          <div class="text-4xl font-bold mb-2">{$weather.temperature.value}°C</div>
          <div class="mb-2">{$weather.description}</div>
          <div class="mb-4">{$weather.city}, {$weather.country}</div>
        </div>
        <div class="grid grid-cols-2 gap-4">
          <div>Wind Speed: {$weather.windSpeed} m/s</div>
          <div>Humidity: {$weather.humidity}%</div>
          <div>UV Index: {$weather.uvIndex}</div>
        </div>
      {:else}
        <p class="text-center">Enter a city name or Indian PIN code to see the weather</p>
      {/if}
    </div>
  </div>
</main>
