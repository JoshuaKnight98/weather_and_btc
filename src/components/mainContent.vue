<template>
  <h1 class="w-3/6 h-auto rounded overflow-hidden mx-96 mt-16 text-center font-bold">Ingrese un pais para saber como esta el clima</h1>
  <div class="w-3/6 rounded overflow-hidden shadow-lg mx-96 mt-4">
    <div>   
      <label for="search" class="mb-2 text-sm font-medium text-gray-900 sr-only dark:text-gray-300">Pais</label>
      <div class="relative">
          <div class="flex absolute inset-y-0 left-0 items-center pl-3 pointer-events-none">
              <svg aria-hidden="true" class="w-5 h-5 text-gray-500 dark:text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg>
          </div>
          <input type="search" id="search" v-model="country" class="block p-4 pl-10 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" placeholder="Pais" required>
          <button @click="getWeather()" class="text-white absolute right-2.5 bottom-2.5 bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">Ver Clima</button>
      </div>
    </div>
    <div v-if="weather != null" :class=" time == 'd'? 'w-full rounded overflow-hidden shadow-lg bg-gradient-to-r from-yellow-200 to-red-400 text-center' : 'w-full rounded overflow-hidden shadow-lg bg-gradient-to-r from-red-500 to-purple-500 text-center text-base'">
      <p class="flex justify-center h-auto items-center w-auto text-lg">El clima en {{country}} es: {{weather.weather[0].main}} <img :src="image"/> </p><br>
      Temperatura: {{weather.main.temp}}°C, Minima: {{weather.main.temp_min}}°C, Maxima: {{weather.main.temp_max}}°C, Humedad: {{weather.main.humidity}} <br>
      Velocidad del viento: {{weather.wind.speed}}m/s, Direccion del Viento: {{weather.wind.deg}}°, Rafagas: {{weather.wind.gust}}m/s
    </div>
  </div>
  <h1 class="w-3/6 h-auto rounded overflow-hidden mx-96 mt-16 text-center font-bold">Conversiones</h1>
  <div class="w-3/6 rounded overflow-hidden shadow-lg mx-96 mt-4">
    <div>   
      <label for="usd" class="mb-2 text-sm font-medium text-gray-900 sr-only dark:text-gray-300">Dolar $</label>
      <div class="relative">
          <input type="number" v-model="usd" id="usd" class="block p-4 pl-10 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" placeholder="$" required>
          <button @click="getPriceBTC(1)" min="0.00" step="0.01" class="text-white absolute right-2.5 bottom-2.5 bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">Convertir a BTC</button>
      </div>
      <div class="w-full rounded overflow-hidden shadow-lg bg-white text-center">
        ฿{{bitcoin}}
      </div>
    </div>
    <div class="mt-8">   
      <label for="btc" class="mb-2 text-sm font-medium text-gray-900 sr-only dark:text-gray-300">Bitcoin ฿</label>
      <div class="relative">
          <input type="number" v-model="btc" id="btc" class="block p-4 pl-10 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" placeholder="฿" required>
          <button type="submit" @click="getPriceBTC(2)" class="text-white absolute right-2.5 bottom-2.5 bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">Convertir a USD</button>
      </div>
      <div class="w-full rounded overflow-hidden shadow-lg bg-white text-center">
        ${{dolar}}
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: 'mainContent',
  data(){
    return{
      weather: null,
      country : '',
      bitcoin : 0.00000000,
      dolar : 0.00,
      tokenWeather: 'ab6c7771f5a33bfafd19566bf10eaa8b',
      image :'',
      time : '',
      usd : null,
      btc : null
    }
  },
  methods:{
    getWeather: function(){

      let URL = `https://api.openweathermap.org/data/2.5/weather?q=${this.country}&appid=${this.tokenWeather}&units=metric`

      axios.post(URL)
       .then((response) => {
          this.weather = response.data;
          this.image = "http://openweathermap.org/img/wn/"+this.weather.weather[0].icon+".png"
          this.time = this.weather.weather[0].icon.substring(2)
        });

    },
    getPriceBTC: function(convert){
      axios.get(
        "https://min-api.cryptocompare.com/data/pricemulti?fsyms=BTC"+
          "&tsyms=USD"
      )
      .then(response => {
        let priceBTCinUSD = response.data.BTC.USD;
        if(convert == 1 ){
          if(this.usd > 0){
            this.bitcoin = parseFloat(this.usd / priceBTCinUSD).toFixed(8);
          }else{
            alert("Debe ingresar un valor mayor a 0")
          }
        }else{
          if(this.btc > 0){
            this.dolar = parseFloat(this.btc * priceBTCinUSD).toFixed(2);
          }else{
            alert("Debe ingresar un valor mayor a 0")
          }
        }
      })
      .catch(error => {
        console.log(error);
      });
    }
  }
}
</script>


