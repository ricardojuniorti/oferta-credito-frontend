<template>
  <div class="formulario-wrapper">
    <form @submit.prevent="consultarOfertas" class="formulario">
      <label for="cpf" class="label-cpf">Digite o CPF:</label>
      <input
        v-model="cpf"
        type="text"
        id="cpf"
        placeholder="000.000.000-00"
        required
        class="input-cpf"
      />
      <button type="submit" class="btn-submit">Consultar Ofertas</button>
    </form>

    <transition name="fade">
      <div v-if="ofertas.length" class="resultado">
        <h3>Top 3 Ofertas</h3>
        <div class="table-wrapper">
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
                <td>R$ {{ oferta.valorSolicitado.toFixed(2) }}</td>
                <td>{{ oferta.qntParcelas }}</td>
                <td>{{ (oferta.taxaJuros * 100).toFixed(2) }}%</td>
                <td>R$ {{ oferta.valorAPagar.toFixed(2) }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </transition>
  </div>
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
.formulario-wrapper {
  max-width: 900px;
  margin: 30px auto;
  padding: 0 15px;
}

.formulario {
  background: #f9fafb;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgb(0 0 0 / 0.1);
  display: flex;
  flex-direction: column;
  gap: 15px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  padding: 20px;
}

.label-cpf {
  font-weight: 600;
  font-size: 1.1rem;
  color: #333;
}

.input-cpf {
  padding: 10px 12px;
  font-size: 1rem;
  border: 1.5px solid #ccc;
  border-radius: 5px;
  transition: border-color 0.3s ease;
}

.input-cpf:focus {
  border-color: #007bff;
  outline: none;
  box-shadow: 0 0 5px #007bff;
}

.btn-submit {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 12px;
  font-size: 1.1rem;
  font-weight: 600;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  align-self: flex-start;
  width: fit-content;
}

.btn-submit:hover {
  background-color: #0056b3;
}

.resultado {
  margin-top: 30px;
}

.resultado h3 {
  margin-bottom: 12px;
  color: #222;
  font-weight: 700;
}

.table-wrapper {
  overflow-x: auto;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgb(0 0 0 / 0.05);
}

table {
  width: 100%;
  border-collapse: collapse;
  min-width: 600px;
  background-color: #1e1e1e;
  color: #ffffff;
}

th,
td {
  padding: 12px 15px;
  border: 1px solid #444;
  text-align: center;
  font-size: 0.95rem;
}

th {
  background-color: #333;
  font-weight: 700;
}

tbody tr:hover {
  background-color: #2a2a2a;
}

/* Fade-in transition */
.fade-enter-active {
  transition: opacity 0.5s ease;
}
.fade-leave-active {
  transition: opacity 0.3s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

@media (max-width: 640px) {
  .formulario {
    margin: 15px 10px;
    padding: 15px;
  }
  .input-cpf {
    font-size: 0.9rem;
  }
  .btn-submit {
    width: 100%;
    font-size: 1rem;
  }
  table {
    min-width: 100%;
    font-size: 0.85rem;
  }
}
</style>
