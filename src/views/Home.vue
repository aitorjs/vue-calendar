<template>
<div>
  <v-layout :wrap="true">
    <v-flex xs12>
      <v-card>
        <v-date-picker 
          v-model="date"
          full-width
          locale="es-ES"
          :min="min"
          :max="max"
          first-day-of-week=1
          @change="getDolar(date)">
        </v-date-picker>
      </v-card>
      <v-card color="error" dark>
        <v-card-text class="display-1 text-center">
          {{value}}
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</div>
</template>

<script>
import axios from 'axios';
import { mapMutations } from 'vuex';

export default {
  name: 'Home',
  data() {
    return {
      date: new Date().toISOString().substr(0, 10),
      min: '1984',
      max: new Date().toISOString().substr(0, 10),
      value: null
    }
  },
  methods: {
    ...mapMutations(['showLoading', 'hideLoading']),
    async getDolar(day) {
      const arrayDay = day.split('-');
      const ddmmyy = arrayDay[2] + '-' + arrayDay[1] + '-' + arrayDay[0];
  
      try {
        this.showLoading({ title: 'Getting info', color: 'primary'})
        const data = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyy}`);
        if (data.data.serie.length > 0) {
          this.value = data.data.serie[0].valor;
        } else {
          this.value = 'No value';
        }
      } catch(e) {
        console.log(e)
      }
      finally {
        this.hideLoading()
      }
    }
  },
  created() {
    this.getDolar(this.date);
  }
}
</script>
