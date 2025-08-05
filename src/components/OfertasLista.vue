<template>
  <div>
    <h2>Ofertas Salvas no Banco</h2>

    <div v-for="grupo in grupos" :key="grupo.cpf" class="grupo">
      <h3>CPF: {{ grupo.cpf }}</h3>

      <div v-for="lote in grupo.lotes" :key="lote.numero_oferta" class="subgrupo">
        <h3>
          CPF: {{ grupo.cpf }} | Oferta Nº {{ lote.numero_oferta }} | Data: {{ lote.data_oferta }}
          <button @click="excluirOferta(lote.numero_oferta)" class="excluir-btn">Excluir</button>
        </h3>
        <table>
          <thead>
            <tr>
              <th>Instituição</th>
              <th>Modalidade</th>
              <th>Valor Solicitado</th>
              <th>Parcelas</th>
              <th>Taxa de Juros</th>
              <th>Valor a Pagar</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(oferta, idx) in lote.ofertas" :key="idx">
              <td>{{ oferta.instituicaoFinanceira }}</td>
              <td>{{ oferta.modalidadeCredito }}</td>
              <td>R${{ parseFloat(oferta.valorSolicitado).toFixed(2) }}</td>
              <td>{{ parseInt(oferta.qntParcelas) }}</td>
              <td>{{ (parseFloat(oferta.taxaJuros) * 100).toFixed(2) }}%</td>
              <td>R${{ parseFloat(oferta.valorAPagar).toFixed(2) }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <BarChart :chart-data="chartData" :chart-options="chartOptions" />
  </div>
</template>

<script>
import axios from 'axios'
import BarChart from './BarChart.vue'

export default {
  components: { BarChart },
  data() {
    return {
      grupos: [],
    }
  },
  mounted() {
    this.buscarOfertasSalvas()
  },
  computed: {
    chartData() {
      const labels = this.grupos.map((g) => g.cpf)
      const data = this.grupos.map((g) =>
        g.lotes.reduce((acc, lote) => acc + (lote.ofertas?.length || 0), 0),
      )

      return {
        labels,
        datasets: [
          {
            label: 'Ofertas',
            backgroundColor: '#42b983',
            data,
          },
        ],
      }
    },
    chartOptions() {
      return {
        responsive: true,
        plugins: {
          legend: { display: false },
          title: {
            display: true,
            text: 'Quantidade de Ofertas por CPF',
          },
        },
      }
    },
  },
  methods: {
    async buscarOfertasSalvas() {
      try {
        const response = await axios.get(
          'http://credito-backend.todeboua.com/api/historico-ofertas',
        )
        console.log('📦 Dados recebidos:', response.data)
        this.grupos = response.data
      } catch (error) {
        console.error('Erro ao buscar ofertas salvas:', error)
        alert('Erro ao buscar ofertas salvas.')
      }
    },
    async excluirOferta(numeroOferta) {
      if (!confirm(`Tem certeza que deseja excluir a oferta nº ${numeroOferta}?`)) return

      try {
        await axios.delete(`http://credito-backend.todeboua.com/api/excluir-oferta/${numeroOferta}`)
        alert(`Oferta nº ${numeroOferta} excluída com sucesso!`)
        this.buscarOfertasSalvas() // Atualiza a lista
      } catch (error) {
        alert('Erro ao excluir a oferta.')
        console.error(error)
      }
    },
  },
}
</script>

<style scoped>
.grupo {
  margin-bottom: 30px;
  border: 1px solid #ddd;
  padding: 10px;
  border-radius: 8px;
}
.subgrupo {
  margin-top: 10px;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 10px;
}
th,
td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: center;
}
th {
  background-color: #f5f5f5;
  color: #2b2929;
}

.excluir-btn {
  background-color: #ff4d4f;
  color: white;
  border: none;
  padding: 4px 10px;
  margin-left: 10px;
  cursor: pointer;
  border-radius: 4px;
  font-size: 0.85rem;
}

.excluir-btn:hover {
  background-color: #d9363e;
}
</style>
