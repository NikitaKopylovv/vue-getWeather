<script setup>
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';


const city = ref('');
const error = ref('');
const info = ref(null);
const cities = {};
const getWeather = () => {
  if (city.value.trim().length < 2) {
    error.value = 'В названии должно быть более 1 символа';
    return;
  }
  error.value = '';

  axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=metric&appid=3d9de74844d28377e81415151cbe6a66`)
    .then(response => {
      info.value = response.data;
    })
    .catch(error => {
      console.error('Ошибка при получении погоды:', error);
      error.value = 'Не удалось получить данные о погоде';
    });
};

onMounted(() => {
  axios.get("https://data-api.oxilor.com/rest/regions?countryCode=US", {
    headers: {
      'Authorization': `Bearer kWtZ-ybgdUeyMbzMbE9KqZfhhemQN5`
    }
  })
    .then(result => {
      cities.value = result.data.edges.map(item => item.node.name);
      console.log("cities.value: ", cities.value);
    })
});

const cityName = computed(() => `'${city.value}'`);
const showTemp = computed(() => `Температура: ${info.value?.main?.temp ?? 'N/A'}`);
const showFeelLikes = computed(() => `Ощущается как: ${info.value?.main?.feels_like ?? 'N/A'}`);
const showMinTemp = computed(() => `Минимальная температура: ${info.value?.main?.temp_min ?? 'N/A'}`);
const showMaxTemp = computed(() => `Максимальная температура: ${info.value?.main?.temp_max ?? 'N/A'}`);

const handleCityChange = () => {
  if (city.value === '') {
    info.value = null; // Сброс информации о погоде при выборе дефолтного значения
  }
};

</script>


<template>
  <div class="wrapper">
    <h1>Погодное приложение</h1>
    <p>Унать погоду в {{ city == "" ? "вашем городе" : cityName }}</p>

    <select v-model="city" @change="handleCityChange">
      <option value="">Выберите город</option>
      <option v-for="(cityItem, index) in cities.value" :key="index" :value="cityItem">{{ cityItem }}</option>
    </select>

    <button v-if="city != ''" @click="getWeather()">Получить погоду</button>
    <button disabled v-else>Выберите город</button>
    <p class="error">{{ error }}</p>



    <div v-if="info != null">
      <p>{{ cityName }}</p>
      <p>{{ showFeelLikes }}</p>
      <p>{{ showTemp }}</p>
      <p>{{ showMinTemp }}</p>
      <p>{{ showMaxTemp }}</p>
    </div>
    <!-- <p v-show="info != null">{{ info?.main?.temp }} градусов цельсия</p> -->
  </div>
</template>

<style scoped>
.wrapper {
  width: 900px;
  height: 500px;
  border-radius: 50px;
  background: #1f0f24;
  padding: 20px;
  text-align: center;
  color: #fff;
}

.wrapper button:disabled {
  background: #746027;
  cursor: not-allowed;
  transition: none;
  transform: none;
}

.wrapper h1 {
  margin-top: 50px;
}

.wrapper p {
  margin-top: 20px;
}

.error {
  color: #d03939;
}

.wrapper input[type="text"],
.wrapper select {
  margin-top: 30px;
  background: transparent;
  border: 0;
  border-bottom: 2px solid #110813;
  color: #fcfcfc;
  font-size: 14px;
  padding: 5px 8px;
  outline: none;
  width: 100%;
  /* Ширина элемента на весь контейнер */
  appearance: none;
  /* Убирает стандартные стрелочки в селекторе */
  -webkit-appearance: none;
  /* Для поддержки вебкит-браузеров */
  -moz-appearance: none;
  /* Для поддержки Firefox */
  cursor: pointer;
  /* Курсор в виде указателя */
}

.wrapper input[type="text"]:focus,
.wrapper select:focus {
  border-bottom-color: #6e2d7d;
}

.wrapper button {
  background: #e3bc4b;
  color: #fff;
  border-radius: 10px;
  border: 2px solid #b99935;
  padding: 10px;
  margin-left: 20px;
  margin-top: 20px;
  cursor: pointer;
  transition: transform 500ms ease;
}

.wrapper button:hover {
  transform: scale(1.1) translateY(-5px);
}
</style>
