<template>
  <main>
    <h1>Consulta de Crédito</h1>
    <OfertasForm />
    <hr />
    <OfertasLista />
  </main>
</template>

<script setup>
import OfertasForm from './components/OfertasForm.vue'
import OfertasLista from './components/OfertasLista.vue'
</script>

<style>
main {
  padding: 20px;
  font-family: Arial, sans-serif;
}
</style>
