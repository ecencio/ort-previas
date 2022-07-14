<template>
  <v-card
    elevation="2"
    tile
    :min-width="widthCalc"
    :max-width="widthCalc"
    min-height="120"

    style="margin: 5px"
    
    @click="cambiarCursada()"
  >
    <v-card-title style="font-size: 1rem; height: 96px;" :class="estilos">
      {{materia.titulo}}
    </v-card-title>
    <v-card-text>
      <div v-if="!materia.compuesta">
      <v-divider/>
        <strong>Previas</strong>
        <v-simple-table dense>
          <template v-slot:default>
            <tbody>
              <tr>
                <td :class="[materia.previas.inscripcion ? 'posible' : 'noPosible', 'previa']">Inscripción / Parcial</td>
                <td :class="[materia.previas.inscripcion ? 'posible' : 'noPosible', 'previa']">Total</td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </div>
      <div v-else>
        <v-row>
          <materia-card v-for="mat in materia.opciones" :key="mat.id" :materia="mat" />
        </v-row>
      </div>
    </v-card-text>
  </v-card>
</template>

<script>
  import MateriaCard from './Materia';
  export default {
    name: 'MateriaCard',

    components: {
      MateriaCard
    },

    props: {
      materia: Object
    },

    data: () => ({
      cursada: 0
    }),

    methods: {
      cambiarCursada() {
        this.cursada = (this.cursada + 1) % 3
      }
    },

    computed: {
      estilos() {
        switch(this.cursada) {
          case 1:
            return 'creditoParcial'
          case 2:
            return 'creditoTotal'
          default:
            return ''
        }
      },
      widthCalc() {
        if(!this.materia.compuesta){
          return '250px'
        }
        return 'auto'
      }
    }
  }
</script>

<style>
  .previa {
    border-radius: 20px;
    text-align: center;
    width: 50%;
  }
  .creditoTotal,
  .posible {
    background-color: #1d831a57;
  }
  .noPosible {
    background-color: #7626346b;
  }
  .creditoParcial {
    background-color: #456fc957;
  }
</style>