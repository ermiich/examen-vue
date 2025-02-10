<script setup>
import { computed, onMounted, ref } from 'vue';
import InsertName from './components/InsertName.vue'
import codes from "./data/codes.json";
import GoogleChart from './components/GoogleChart.vue';

const selectedCodes = ref([]);
const selectedCountry = ref('');
const currentCountryData = ref(null);
const countriesNamesWithCodes = ref([]);

const dataTypes = ['population', 'pib', 'area', 'income'];
const selectedDataType = ref('population');

const dataToGchart = ref([null, null]);


async function fetchInfoByCode(code) {
  const response = await fetch('http://localhost:3000/api/country/' + code);
  const data = await response.json();
  return data ? data : null;
}

function dataToChart() {
  let aux = [];
  selectedCodes.value.forEach(code => {
    const name = countriesNamesWithCodes.value[code];
    const data = currentCountryData.value[selectedDataType.value];
    aux.push([name, data]);
  });

  console.log(aux);
}

const newSelectedHandler = async (code) => {
  if (!selectedCodes.value.includes(code)) {
    const data = await fetchInfoByCode(code);
    if (data) {
      selectedCountry.value = code;
      selectedCodes.value.push(code);
      console.log(data);
      currentCountryData.value = data;
      return;
    }
    console.log('Pais no encontrado ' + code);

  }
};
const removeSelected = (code) => {
  selectedCodes.value = selectedCodes.value.filter((c) => c !== code);
};
onMounted(async () => {
  const response = await fetch('http://localhost:3000/api/names');
  const data = await response.json();
  console.log(data);
  countriesNamesWithCodes.value = data;
  // Array.sort(countriesNamesWithCodes.value, (a, b) => a.localeCompare(b));
})
</script>

<template>
  <div class="parent">
    <div class="header">
      <h1>Datos mundiales</h1>
    </div>
    <div class="name">
      <InsertName />
    </div>
    <div class="div-codes">
      <ul>
        <li v-for="(code, index) in codes" :key="index" @click="newSelectedHandler(code)">{{
          countriesNamesWithCodes[code] }}</li>
      </ul>
    </div>
    <div class="selected">

      <p v-if="selectedCodes.length > 0">
      <h2>Códigos seleccionados {{ selectedCodes.length }}</h2>
      <ul>
        <li v-for="(code, index) in selectedCodes" :key="index">{{ countriesNamesWithCodes[code] }}
          <button @click="removeSelected(code)">Desmarcar</button>
        </li>
      </ul>
      </p>

      <p v-else>Debes seleccionar algún pais en el panel de códigos.</p>
    </div>
    <div class="country-data">
      <p v-if="selectedCountry.length > 0">
      <table>
        <tr>
          <th>Nombre:</th>
          <td>{{ currentCountryData.name }}</td>
          <th>Población:</th>
          <td>{{ currentCountryData.population * 1000000 }} millones</td>
        </tr>
        <tr>
          <th>PIB:</th>
          <td>{{ currentCountryData.pib }} millones</td>
          <th>Área:</th>
          <td>{{ currentCountryData.area }} km2</td>
        </tr>
        <tr>
          <th>Renta:</th>
          <td>{{ currentCountryData.income }}</td>
          <th>PIB/Habitante:</th>
          <td>{{ parseFloat(currentCountryData.pib / currentCountryData.population).toFixed(2) }}</td>
        </tr>
        <tr>
          <th><button>Eliminar</button></th>

        </tr>

      </table>
      </p>
      <p v-else>No hay datos que mostrar</p>
    </div>
    <div class="options">
      <p>
        <label v-for="(option, index) in dataTypes" :key="index"><input type="radio" name="option"
            @click="dataToChart">{{
              option
            }}</label>
      </p>
    </div>
    <div class="chart">
      <GoogleChart :data="selectedDataToChart" />
    </div>
  </div>
</template>

<style scoped>
.parent {
  background-color: black;
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: auto repeat(3, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
  height: 100vh;
  margin: -2rem;
}

.header {
  grid-area: 1 / 1 / 2 / 6;
  background-color: aquamarine;
}

.header h1 {
  margin: 2px;
}

.div-codes {
  grid-area: 2 / 1 / 6 / 2;
  background-color: azure;
  overflow-y: auto;
}

.name {
  grid-area: 2 / 2 / 3 / 3;
  background-color: antiquewhite;
}

.selected {
  grid-area: 2 / 5 / 4 / 6;
  background-color: lightyellow;
  overflow-y: auto;
}

.country-data {
  grid-area: 2 / 3 / 3 / 5;
  background-color: lightseagreen;
}

.options {
  grid-area: 3 / 2 / 4 / 5;
  background-color: bisque;
}

.chart {
  grid-area: 4 / 2 / 5 / 6;
  background-color: aqua;
}

ul li {
  text-align: left;
  font-size: 0.9rem;
}

ul li:hover {
  font-weight: bold;
  cursor: pointer
}

ul li button {
  font-size: 0.6rem;
  margin: 2px;
}

h2 {
  font-size: 1.2rem;
}
</style>