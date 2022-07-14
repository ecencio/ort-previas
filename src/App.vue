<template>
  <v-app>
    <v-app-bar
      app
      color="#661020"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="ORT"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="./assets/Logo_ORT_alpha.png"
          width="100"
        />
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-main>
      <v-container fluid >
        <v-row justify="center" align-items="center">
          <div class="token">
            <v-text-field
              label="Token"
              style="width: 350px;"
              v-model="token"
            ></v-text-field>
            <v-btn
              :disabled="!token"
              :dark="!!token"
              color="#661020"
              elevation="2"
              style="margin-left: 3px"
              @click="actualizarRequisitos"
              :loading="cargando"
            >Buscar</v-btn>
          </div>
        </v-row>

        <div v-for="semestre in plan" :key="semestre.semestre">
            <v-row justify="center" style="margin-top: 10px">
              <h2>Semestre: {{semestre.semestre}}</h2>
            </v-row>
            <v-row justify="center">
              <materia-card v-for="materia in semestre.materias" :key="materia.id" :materia="materia" />
            </v-row>
        </div>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Planes from './planes.json'
import MateriaCard from './components/Materia'
import axios from 'axios'

let actualizarMateria = async (materia, token) => {
  if(materia.id > 0){
    return new Promise(resolve => {
      axios.get(`https://gestionapi.ort.edu.uy/ORTSecure/PerfilAcademico/MateriaDelTitulo/PreviasDeMateria?idMateria=${materia.id}&idTitulo=2485`, {
        headers: {
          "x-token": token
        }
      })
      .then(resp => {
        materia.previas.inscripcion = resp.data[0].Resultado === 'T';
        materia.previas.aprobacion = resp.data[1].Resultado === 'T';
        resolve(true)
      })
      .catch(err => {
        console.error(err)
        resolve(false)
      })
    })    
  }
}

export default {
  name: 'App',

  components: {
    MateriaCard
  },

  data: () => ({
    plan: Planes.Plan2019,
    token: '',
    cargando: false
  }),

  methods: {
    async actualizarRequisitos() {
      this.cargando = true;
      for(let semestre of this.plan){
        for(let materia of semestre.materias){
          if(!materia.compuesta){
            await actualizarMateria(materia, this.token)
          }
          else{
            for(let mat of materia.opciones){
              await actualizarMateria(mat, this.token)
            }
          }
        }
      }
      this.cargando = false;
    }
  }
};
</script>

<style>
  .token {
    width: 400px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>