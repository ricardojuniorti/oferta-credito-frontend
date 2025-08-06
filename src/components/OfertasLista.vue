<template>
  <div class="container">
    <h2>📦 Ofertas Salvas no Banco</h2>

    <div v-for="grupo in grupos" :key="grupo.cpf" class="grupo">
      <h3 class="cpf-title">CPF: {{ grupo.cpf }}</h3>

      <div v-for="lote in grupo.lotes" :key="lote.numero_oferta" class="subgrupo">
        <div class="header-lote">
          <h4>Oferta Nº {{ lote.numero_oferta }}</h4>
          <button @click="excluirOferta(lote.numero_oferta)" class="excluir-btn">Excluir</button>
        </div>

        <div class="table-wrapper">
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
                <td>R$ {{ parseFloat(oferta.valorSolicitado).toFixed(2) }}</td>
                <td>{{ parseInt(oferta.qntParcelas) }}</td>
                <td>{{ (parseFloat(oferta.taxaJuros) * 100).toFixed(2) }}%</td>
                <td>R$ {{ parseFloat(oferta.valorAPagar).toFixed(2) }}</td>
              </tr>
            </tbody>
          </table>
        </div>
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

      const backgroundColors = labels.map(() => {
        // Gera cor aleatória no estilo pastelzinho suave
        const hue = Math.floor(Math.random() * 360)
        return `hsl(${hue}, 70%, 60%)`
      })

      return {
        labels,
        datasets: [
          {
            label: 'Ofertas por CPF',
            data,
            backgroundColor: backgroundColors,
          },
        ],
      }
    },
    chartOptions() {
      return {
        responsive: true,
        plugins: {
          legend: {
            position: 'bottom',
            labels: {
              color: '#fff', // cor do texto da legenda (dark mode)
            },
          },
          title: {
            display: true,
            text: 'Distribuição de Ofertas por CPF',
            color: '#fff',
            font: {
              size: 18,
            },
          },
        },
      }
    },
  },

  methods: {
    async buscarOfertasSalvas() {
      try {
        const response = await axios.get(
          'https://credito-backend.todeboua.com/api/historico-ofertas',
        )
        this.grupos = response.data
      } catch (error) {
        console.error('Erro ao buscar ofertas salvas:', error)
        alert('Erro ao buscar ofertas salvas.')
      }
    },
    async excluirOferta(numeroOferta) {
      if (!confirm(`Tem certeza que deseja excluir a oferta nº ${numeroOferta}?`)) return

      try {
        await axios.delete(
          `https://credito-backend.todeboua.com/api/excluir-oferta/${numeroOferta}`,
        )
        alert(`Oferta nº ${numeroOferta} excluída com sucesso!`)
        this.buscarOfertasSalvas()
      } catch (error) {
        alert('Erro ao excluir a oferta.')
        console.error(error)
      }
    },
  },
}
</script>

<style scoped>
.container {
  max-width: 900px;
  margin: 30px auto;
  padding: 0 15px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  color: #fff;
  background-color: #121212;
}

h2 {
  text-align: center;
  margin-bottom: 25px;
  font-size: 1.8rem;
  font-weight: 700;
  color: #fff;
}

.grupo {
  border: 1px solid #333;
  border-radius: 8px;
  padding: 15px;
  margin-bottom: 25px;
  background-color: #1e1e1e;
}

.cpf-title {
  font-size: 1.1rem;
  font-weight: 600;
  color: #bbb;
}

.subgrupo {
  margin-top: 15px;
}

.header-lote {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  flex-wrap: wrap;
  gap: 8px;
}

.header-lote h4 {
  margin: 0;
  font-weight: 600;
  font-size: 1rem;
  color: #ddd;
}

.excluir-btn {
  background-color: #ff4d4f;
  color: white;
  border: none;
  padding: 6px 14px;
  border-radius: 5px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.excluir-btn:hover {
  background-color: #d9363e;
}

.table-wrapper {
  overflow-x: auto;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgb(0 0 0 / 0.3);
}

table {
  width: 100%;
  border-collapse: collapse;
  min-width: 700px;
  background-color: #1e1e1e;
  color: #fff;
}

th,
td {
  border: 1px solid #444;
  padding: 10px 12px;
  text-align: center;
  font-size: 0.9rem;
}

th {
  background-color: #333;
  font-weight: 700;
  color: #fff;
}

tbody tr:hover {
  background-color: #2a2a2a;
}

@media (max-width: 700px) {
  .header-lote {
    flex-direction: column;
    align-items: flex-start;
  }

  .excluir-btn {
    width: 100%;
    padding: 8px 0;
  }

  table {
    font-size: 0.8rem;
  }

  th,
  td {
    padding: 8px;
  }
}
</style>
