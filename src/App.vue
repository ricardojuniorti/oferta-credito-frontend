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

  display: flex;
  flex-direction: column;
  align-items: center; /* centraliza horizontalmente */
  max-width: 700px; /* largura máxima pra ficar mais agradável */
  margin: 0 auto; /* centraliza o main no body */
}

h1 {
  text-align: center;
  margin-bottom: 20px;
}
</style>
