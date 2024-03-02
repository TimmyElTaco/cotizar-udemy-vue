<script setup>
  import { ref, computed, watch } from 'vue'
  import Header from './components/Header.vue';
  import Button from './components/Button.vue';
  import { calcularTotalPagar } from './utility';
  
  const MIN = 0;
  const MAX = 20000;
  const STEP = 100;
  
  const cantidad = ref(10000);
  const meses = ref(6);
  const total = ref(0);

  const formatearDinero = cantidad => {
    const formatter = new Intl.NumberFormat('en-US', {
      style:'currency',
      currency:'USD'
    });

    return formatter.format(cantidad)
  };

  watch([cantidad, meses], () => {
    total.value = calcularTotalPagar(cantidad.value, meses.value);
  })

  const pagoMensual = computed( () => {
    return total.value / meses.value;
  } )

  const handleChangeDec = () => {
    
    const valor = cantidad.value - STEP;

    if(valor < MIN) {
      alert('Cantidad no valida');
      return; 
    }

    cantidad.value = valor;

  }

  const handleChangeInc = () => {
    
    const valor = cantidad.value + STEP;

    if(valor > MAX) {
      alert('Cantidad no valida');
      return; 
    }

    cantidad.value = valor;
  }

</script>

<template>
  <div class="my-20 max-w-lg mx-auto bg-white shadow p-10 rounded-xl">
    <Header />

    <div class="flex justify-between mt-10">
      <Button
        :operador="'-'"
        @handleChange="handleChangeDec"
      />
      <Button
        :operador="'+'"
        @handleChange="handleChangeInc"
      />
    </div>

    <div class="my-5">
      <input 
        type="range"
        class="w-full bg-gray-300 accent-lime-500 hover:accent-lime-600"
        v-model.number="cantidad"
        :max="MAX"
        :min="MIN"
        :step="STEP"
      />
      <p class="text-center my-10 text-5xl font-extrabold text-indigo-600">
        {{ formatearDinero(cantidad) }}
      </p>
      <h2 class="font-extrabold text-gray-500 text-center text-2xl">
        Elige un <span class="text-indigo-600">plazo</span> a pagar.
      </h2>
      <select
        class="w-full p-2 bg-white border border-gray-300 rounded-lg text-center text-xl font-bold text-gray-500 mt-5"
        :value="meses"
        v-model.number="meses"
      >
        <option value="6">6 meses</option>
        <option value="12">12 meses</option>
        <option value="24">24 meses</option>
      </select>
    </div>
    <div v-if="total > 0" class="my-5 space-y-3 bg-gray-50 p-5">
      <h2 class="text-2xl font-extrabold text-gray-500 text-center">
        Resumen <span class="text-indigo-600">de pagos</span>
      </h2>
      <p class="text-xl text-gray-500 text-center font-bold">{{ meses }} Meses</p>
      <p class="text-xl text-gray-500 text-center font-bold">Total a pagar: {{ formatearDinero(total) }}</p>
      <p class="text-xl text-gray-500 text-center font-bold">Mensuales {{ formatearDinero(pagoMensual) }}</p>
    </div>
    <p v-else class="text-center text-lg text-gray-600 font-bold">Agrega una cantidad y un plazo para calcular el total a pagar.</p>
  </div>
</template>
