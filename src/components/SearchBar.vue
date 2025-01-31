<template>
  <div class="container">
    <h1>⚽ Búsqueda de Equipos ⚽</h1>

    <input
      type="text"
      v-model="query"
      @input="debouncedFetchResults"
      placeholder="Escribe el nombre del equipo..."
    />

    <ul v-if="results.length" class="suggestions">
      <li
        v-for="equipo in results"
        :key="equipo.id_equipo"
        @click="selectResult(equipo)"
      >
        {{ equipo.nombre }}
      </li>
    </ul>

    <button @click="buscarEquipo">Buscar</button>

    <transition name="fade">
      <div v-if="selectedEquipo" class="card">
        <h2>Equipo Seleccionado</h2>
        <p><strong>ID Equipo:</strong> {{ selectedEquipo.id_equipo }}</p>
        <p><strong>Nombre:</strong> {{ selectedEquipo.nombre }}</p>
      </div>
    </transition>

    <transition name="fade">
      <div v-if="campeonato" class="card">
        <h2>🏆 Campeonatos Ganados 🏆</h2>
        <p>
          <strong>Copas Nacionales:</strong> {{ campeonato.copas_nacionales }}
        </p>
        <p>
          <strong>Copas Internacionales:</strong>
          {{ campeonato.copas_internacionales }}
        </p>
      </div>
    </transition>
  </div>
</template>

<script>
import axios from "axios";
import _ from "lodash";

export default {
  data() {
    return {
      query: "",
      results: [],
      equipos: [],
      selectedEquipo: null,
      campeonato: null,
    };
  },
  async created() {
    this.debouncedFetchResults = _.debounce(this.fetchResults, 300);
    await this.getEquipos();
  },
  methods: {
    async getEquipos() {
      try {
        const response = await axios.get("http://127.0.0.1:5000/equipos");
        this.equipos = response.data;
      } catch (error) {
        console.error("Error al obtener la lista de equipos", error);
      }
    },
    fetchResults() {
      if (this.query.length < 2) {
        this.results = [];
        return;
      }

      this.results = this.equipos.filter((equipo) =>
        equipo.nombre.toLowerCase().includes(this.query.toLowerCase())
      );
    },
    selectResult(equipo) {
      this.query = equipo.nombre;
      this.selectedEquipo = equipo;
      this.results = [];
      this.getCampeonato(equipo.id_equipo);
    },
    async buscarEquipo() {
      const equipoEncontrado = this.equipos.find(
        (equipo) => equipo.nombre.toLowerCase() === this.query.toLowerCase()
      );

      if (equipoEncontrado) {
        this.selectedEquipo = equipoEncontrado;
        await this.getCampeonato(equipoEncontrado.id_equipo);
      } else {
        alert("No se encontró el equipo.");
        this.selectedEquipo = null;
        this.campeonato = null;
      }
    },
    async getCampeonato(id_equipo) {
      try {
        const response = await axios.get(`http://127.0.0.1:5000/campeonatos`);
        const campeonatos = response.data;

        this.campeonato = campeonatos.find(
          (campeonato) => campeonato.id_equipo === id_equipo
        );

        if (!this.campeonato) {
          this.campeonato = null;
        }
      } catch (error) {
        console.error("Error al obtener el campeonato", error);
        this.campeonato = null;
      }
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Oswald:wght@400;700&display=swap");

.container {
  max-width: 500px;
  margin: 20px auto;
  text-align: center;
  font-family: "Oswald", sans-serif;
  background: #f5f5f5;
  padding: 20px;
  border-radius: 15px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
}

h1 {
  font-size: 24px;
  color: #2c3e50;
  margin-bottom: 20px;
}

input {
  width: 90%;
  padding: 10px;
  border-radius: 8px;
  border: 2px solid #3498db;
  font-size: 16px;
  text-align: center;
  margin-bottom: 10px;
}

.suggestions {
  list-style: none;
  padding: 0;
  margin: 0;
  background: white;
  border: 1px solid #ccc;
  max-height: 150px;
  overflow-y: auto;
  position: absolute;
  width: 85%;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;
}

.suggestions li {
  padding: 10px;
  cursor: pointer;
  border-bottom: 1px solid #ddd;
}

.suggestions li:hover {
  background: #f4f4f4;
}

button {
  padding: 10px 20px;
  background: #27ae60;
  color: white;
  border: none;
  font-size: 16px;
  font-weight: bold;
  border-radius: 8px;
  cursor: pointer;
  transition: 0.3s ease-in-out;
}

button:hover {
  background: #2ecc71;
  transform: scale(1.05);
}

.card {
  background: white;
  padding: 15px;
  margin-top: 15px;
  border-radius: 10px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.card h2 {
  color: #e74c3c;
  font-size: 22px;
}

p {
  font-size: 18px;
  color: #34495e;
}

/* Animaciones */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
