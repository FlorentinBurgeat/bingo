<template>
  <div id="app">
    <h1 class="title">Bingo Imago Generator</h1>

  <div class="columns">
    <div class="column">
      <h2 class="subtitle is-size-4 has-text-primary"> Règles</h2>
      <ul>
        <li>A chaque Round tout le monde regénère un Bingo vide</li>
        <li>Un nouveau Round commence chaque Lundi, on regénère son bingo même si il n'y a pas eu de gagnant au round précédent.</li>
        <li>Lorsque vous constatez <span class="has-text-danger">(PAS QUELQU'UN D'AUTRE QUI LE DIT SUR SLACK)</span> un évènement de votre bingo, cochez une case de votre bingo. Quelqu'un d'autre que la personne sur l'évènement doit le confirmer pour être valide.</li>
        <li>Une fois que vous avez 5 cases à la suite (ligne, colonne, diagonale) cochée, faites un screen et envoyez le dans le slack en mettant "BINGO"</li>
      </ul>

      <h2 class="subtitle is-size-4 has-text-primary">Gains</h2>
      <h3 class="is-size-5 has-text-info">Premier</h3>
      <p>Le droit de croissantiser quelqu'un ET un totem d'immunité</p>
      <h3 class="is-size-5 has-text-info">Deuxième</h3>
      <p>Au choix:
        <ul>
          <li>Un totem d'immunité pour le round courant</li>
          <li>Une case offerte au prochain round</li>
        </ul>
      </p>
      <h3 class="is-size-5 has-text-info">Troisième</h3>
      <p>Un café (ou chocolat) payé par le croissantisé</p>
      <h3 class="is-size-5 has-text-info">Croissantisé</h3>
      <p>Le devoir de ramener à manger pour la pause et de payer le café du troisième (s'il y en a un)</p>
    </div>
    <div class="column">

      <div class="field is-horizontal player-selection">
        <div class="field-label">
          <label for="select-joueur" class="label">Sélectionne toi</label>
        </div>
        <div class="field-body">
          <div class="field has-addons">
            <div class="control">
              <div class="select">
                <select id="select-joueur" v-model="selectedPlayer">
                  <option v-for="player in players" :key="player">{{player}}</option>
                </select>
              </div>
            </div>
            <div class="control"><button class="button is-primary" @click="generatebingoEvents()">Générer</button></div>
          </div>
        </div>
      </div>

      <table class="table is-bordered">
        <thead></thead>
        <tr>
          <th colspan="5">
            <div class="columns">
              <div class="column">
                <div class="columns">
                  <div class="column bin"><h1 class="title">BIN</h1></div>
                </div>
                <div class="columns">
                  <div class="column ima"><h1 class="title">IMA</h1></div>
                </div>
              </div>
              <div class="column go"><h1 class="title">GO</h1></div>
            </div>
          </th>
        </tr>
        <tbody class="bingo-body">
          <tr v-for="row in 5" :key="'row' + row">
            <td v-for="column in 5" :key="'column' + column">
              <span>
                {{bingoEvents[column - 1 + (row - 1) * 5]}}
              </span>
            </td>
          </tr>
        </tbody>
      </table>

    </div>
  </div>


  </div>
</template>

<script>
import players from './assets/players.json'
import events from './assets/events.json'

export default {
  name: 'app',
  data () {
    return {
      events,
      players,
      selectedPlayer: "",
      bingoEvents: []
    }
  },
  computed: {
    myEvents () {
      return this.events.filter(e => e.player !== this.selectedPlayer)
    }
  },
  methods: {
    generatebingoEvents () {
      const myEventsCopy = this.myEvents.slice();
      const bingoArray = []

      for(let i=0; i < 25 ; i++) {
        if (myEventsCopy.length > 0) {
          const randomValue = Math.floor(Math.random() * myEventsCopy.length)
          bingoArray.push(myEventsCopy[randomValue])
          myEventsCopy.splice(randomValue, 1)
        } else bingoArray.push({event: ''})
      }
      this.bingoEvents = bingoArray.map(e => e.event)
    }
  }
}
</script>

<style lang="scss">
 // @import "~font-awesome/css/font-awesome.css";
 // @import "~bulma/sas";
#app {
  margin-left: 50px;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

.player-selection {
  position: relative;
  left: 0px;
}


.table tbody.bingo-body {
  background-image: url("./assets/croissant.svg");
  background-repeat: no-repeat;
  tr {
    height: 100px;
    td {
      width: 100px;
      font-size: 12px;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      text-align: center;
      vertical-align: middle;
    }
  }
}

.croissant-img {
  position: absolute;
}

.bin,.ima {
  text-align: right;
}
.bin {
  padding-bottom: 0px;
  vertical-align: bottom;
}
.ima{
  padding-top: 0px;
  vertical-align: top;
}
.go {
  text-align: left;
  .title {
    vertical-align: middle;
    font-size: 72px;
  }
}

ul {
  list-style-type: disc;
  margin-left: 20px;
  li {
    margin-bottom: 20px;
    width: 500px;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    text-align: justify;
  }
}
</style>
