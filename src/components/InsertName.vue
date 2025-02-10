<script setup>
import { ref } from 'vue';

const insertedname = ref('');
</script>
<template>
  <p>Inserta tu nombre:
    <input type="text" v-model="insertedname">
  </p>
  <p v-if="insertedname">Tu nombre <strong>{{ insertedname }}</strong> tiene <strong>{{ insertedname.length }}
    </strong> letras
  </p>
</template>



<style scoped></style>