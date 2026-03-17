<script setup>
import ListaEquipajeEspecial from './components/ListaEquipajeEspecial.vue'
</script>

<template>
  <header>
    <h1>Módulo de Operaciones del Aeropuerto JJ</h1>
  </header>
  <main>
    <ListaEquipajeEspecial />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
  background-color: #f1f1f1;
  padding: 10px;
  text-align: center;
}
main {
  padding: 20px;
}
</style>