<script setup>
import iconEval from './icons/IconEvals.vue'
import iconFaltante from './icons/IconFaltantes.vue'

import dayjs from 'dayjs'

import { ref, onMounted, computed } from 'vue';

const isLoading = ref(true);
const info = ref({
    periodo: '',
    alEvaluados: '',
    alTotal: '',
    inicio: '',
    fin: ''
})

// Crear un nuevo locale para dayjs
const myLocale = {
    name: 'mi-locale',
    weekdays: ['Domingo', 'Lunes', 'Martes', 'Miércoles', 'Jueves', 'Viernes', 'Sábado'],
    months: ['Enero', 'Febrero', 'Marzo', 'Abril', 'Mayo', 'Junio', 'Julio', 'Agosto', 'Septiembre', 'Octubre', 'Noviembre', 'Diciembre'],
    // ... otras propiedades específicas de tu locale
};
dayjs.locale(myLocale.name, myLocale);

const currentDate = ref(dayjs().format('dddd DD [de] MMMM [de] YYYY'));

const tiempoRestante = computed(() => {
    const date1 = dayjs(info.value.inicio);
    const date2 = dayjs(info.value.fin);
    return date2.diff(date1, 'h');
})

const porcAvance = computed(() => {
    return Math.round(info.value.alEvaluados / info.value.alTotal * 100)
})

const faltantes=computed(()=>{
    return info.value.alTotal-info.value.alEvaluados
})

const porcFaltas = computed(() => {
    const faltantes=info.value.alTotal-info.value.alEvaluados
    return Math.round(faltantes / info.value.alTotal * 100)
})

async function fetchData() {
    try {
        const response = await fetch('https://sitmotul.com.mx/api/statusEval');
        const data = await response.json();
        info.value.periodo = data.periodo;
        info.value.alTotal = data.alTotal;
        info.value.alEvaluados = data.alEvaluados;
        info.value.inicio = data.inicio;
        info.value.fin = data.fin;
        isLoading.value = false;
    } catch (error) {
        console.error('Error al obtener datos:', error);
    }
}

onMounted(() => {
    fetchData();
    

    // Actualizar cada 1 minuto
    setInterval(fetchData, 60000);
})



</script>

<template>
    <div v-if="isLoading">Cargando datos...</div>
    <div v-else>
        <h2 class="font-medium text-center mb-5">Estado de la evaluación tutor del {{ info.periodo }}
            <br>
            al {{ currentDate }}
        </h2>
        <div class="container w-full mx-auto bg-amber-500 px-10 py-3 text-center shadow-md ">
            <p><span class="text-2xl font-medium">{{ tiempoRestante }} h</span><br> para finalizar</p>
        </div>
        <br>
        <div class="container w-3/4 mx-auto bg-sky-800 rounded-lg text-sky-200 px-10 py-8 shadow-md">
            <div class="flex gap-2 justify-evenly mb-2">
                <h3 class="text-5xl font-bold">{{ porcAvance }} %</h3>
                <iconEval></iconEval>
            </div>
            <p class="text-center">{{ info.alEvaluados }} de {{ info.alTotal }} completadas</p>
        </div>
        <br>
        <div class="container w-3/4 mx-auto bg-red-800 rounded-lg text-sky-200 px-10 py-8 shadow-md">
            <div class="flex gap-2 justify-evenly mb-2">
                <h3 class="text-5xl font-bold">{{ porcFaltas }} %</h3>
                <iconFaltante></iconFaltante>
            </div>
            <p class="text-center">{{ faltantes }} faltantes</p>
        </div>
    </div>
</template>