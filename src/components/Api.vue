<template>
  <div class="api">
    <h1>Conversão de moedas</h1>
    <div id="label">
      <label for="valueToConvert">Valor a ser convertido:</label><br>
      <input id="valueToConvert" class="margin-2" type="number" v-model="valueToConvert"><br>
    </div>

    <div id="optionsOrigin">
      <label>Moeda origem:</label><br>
      <select class="currency-options" @change="changeCurrencyOrigin($event)">
        <option value="" select disabled>Escolha</option>
        <option v-for="currency in currencies" :value="currency.id" :key="currency.id"> {{ currency.name }}</option>
      </select>
    </div>

    <div id="optionsDestiny">
      <label>Moeda destino:</label><br>
      <select class="currency-options" @change="changeCurrencyDestiny($event)">
        <option value="" select disabled>Escolha</option>
        <option v-for="currency in currencies" :value="currency.id" :key="currency.id"> {{ currency.name }}</option>
      </select>
    </div>

    <div id="result">
      <button v-on:click="convert()">Converte</button>
      <p><span>Valor da conversão: {{ result }}</span></p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Api',
  data () {
    return {
      apiUrl: 'https://openexchangerates.org/api/',
      appid: 'ad5cf615c2f940dc8cf1ca03d7a0c8b4',
      valueToConvert: 0,
      currencies: [],
      rates: [],
      keyFrom: null,
      keyTo: null,
      result: null,
      selectedOptionOrigin: null,
      selectedOptionDestiny: null

    }
  },
  mounted () {
    axios.get(this.apiUrl + 'currencies.json').then((response) => {
      let json_object = response.data
      for (const [key, value] of Object.entries(json_object)) {
        let name = `${key.toString()}: ${value.toString()}`
        this.currencies.push({name, id: key})
      }
      this.keyFrom = this.currencies[0].name.split(':')[0]
      this.keyTo = this.currencies[0].name.split(':')[0]
      this.selectedOptionOrigin = this.currencies[0].name,
      this.selectedOptionDestiny = this.currencies[0].name
    })

    axios.get(this.apiUrl + `latest.json?app_id=${this.appid}`).then((response) => {
      let rates = response.data.rates
      for (const [key, value] of Object.entries(rates)) {
        let name = `${key.toString()}: ${value.toString()}`
        this.rates.push({name, id: `${key}-`})
      }
    })
  },
  methods: {
    changeCurrencyOrigin (event) {
      const text = event.target.options[event.target.options.selectedIndex].text
      this.keyFrom = text.split(':')[0]
      this.selectedOptionOrigin = text
    },
    changeCurrencyDestiny (event) {
      const text = event.target.options[event.target.options.selectedIndex].text
      this.keyTo = text.split(':')[0]
      this.selectedOptionDestiny = text
    },
    formatter () {
      new Intl.NumberFormat('pt-BR', {
        style: 'currency',
        currency: this.keyTo
      })
    },
    convert() {

      if( this.valueToConvert > 0) {
        let valueInUSD = null
        let value2InUSD = null
        let convertedValue = null
      
        for (let i in this.rates) {
          let id = this.rates[i].id
          if (id.substring(0, id.length - 1) === this.keyFrom ) {
            valueInUSD = parseFloat(this.rates[i].name.split(':')[1])
          } else if (id.substring(0, id.length - 1) === this.keyTo) {
            value2InUSD = parseFloat(this.rates[i].name.split(':')[1])
          }
        }

        convertedValue = this.valueToConvert * (value2InUSD / valueInUSD)
        this.result = new Intl.NumberFormat('pt-br', { style: 'currency', currency: this.keyTo }).format(convertedValue);

      }
      
    }
  }
}
</script>

<style scoped>
.api {
  min-width: 100%;
}

#label, #optionsOrigin, #optionsDestiny, #result {
  text-align: left;
  margin: 10px;
}
</style>

