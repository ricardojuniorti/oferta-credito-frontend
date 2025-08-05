// src/components/OfertasForm.vue
<template>
  <form @submit.prevent="consultarOfertas" class="formulario">
    <label for="cpf">Digite o CPF:</label>
    <input v-model="cpf" type="text" id="cpf" placeholder="000.000.000-00" required />
    <button type="submit">Consultar Ofertas</button>

    <div v-if="ofertas.length">
      <h3>Top 3 Ofertas</h3>
      <table>
        <thead>
          <tr>
            <th>Instituição</th>
            <th>Modalidade</th>
            <th>Valor Solicitado</th>
            <th>Parcelas</th>
            <th>Taxa de Juros</th>
            <th>Valor Final</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(oferta, index) in ofertas" :key="index">
            <td>{{ oferta.instituicaoFinanceira }}</td>
            <td>{{ oferta.modalidadeCredito }}</td>
            <td>R${{ oferta.valorSolicitado.toFixed(2) }}</td>
            <td>{{ oferta.qntParcelas }}</td>
            <td>{{ (oferta.taxaJuros * 100).toFixed(2) }}%</td>
            <td>R${{ oferta.valorAPagar.toFixed(2) }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </form>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      cpf: '',
      ofertas: [],
    }
  },
  methods: {
    async consultarOfertas() {
      try {
        const response = await axios.post(
          'https://credito-backend.todeboua.com/api/gerar-ofertas',
          {
            cpf: this.cpf,
          },
        )
        this.ofertas = response.data
      } catch (error) {
        alert('Erro ao buscar ofertas. Verifique o CPF ou a API.')
        console.error(error)
      }
    },
  },
}
</script>

<style scoped>
.formulario {
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-width: 600px;
  margin: auto;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}
th,
td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: center;
}
th {
  background-color: #eee;
}
</style>
