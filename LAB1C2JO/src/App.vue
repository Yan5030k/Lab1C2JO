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
  background: linear-gradient(135deg, #0f172a, #1e3a8a);
  color: white;
  padding: 28px 20px;
  text-align: center;
  border-bottom-left-radius: 22px;
  border-bottom-right-radius: 22px;
  box-shadow: 0 8px 24px rgba(15, 23, 42, 0.18);
}

header h1 {
  margin: 0;
  font-size: 32px;
  font-weight: 800;
  letter-spacing: 0.4px;
}

main {
  padding: 30px 24px;
  max-width: 1300px;
  margin: 0 auto;
}
</style>