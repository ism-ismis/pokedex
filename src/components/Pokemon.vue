<template v-slot:default="{ pending, error, data}">
<!-- Request Pending -->
  <div>
    <button v-on:click="handleBack()" type="button" class="return btn btn-danger btn-sm">return</button>
    <div v-if="pending" class="text-center">
      Loading ...
    </div>
<!-- Request Error -->
    <div v-else-if="error" role="alert">
      {{ error }}
    </div>
<!-- Request Success -->
    <div
      v-else
      class="pokemon">
      <div class="card-body">
        <img class="card-img" :src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/' + this.$route.params.id + '.png'" />
        <img class="card-img" :src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/back/' + this.$route.params.id + '.png'" />
        <h2 class="card-title">{{PokemonDetail.name.toUpperCase()}}</h2>
        <h4><strong>{{PokemonSpecies.names[0].name}}</strong></h4>
        <table class="poke-table" border="1">
          <tr>
            <td>ID</td><td>{{PokemonDetail.id}}</td>
          </tr>
          <tr
            v-for="(type, index) in jaTypes"
            v-bind:key="index"
          >
            <td>タイプ{{index + 1}}</td>
			<td>{{jaTypes[index]}}</td>
          </tr>
          <tr
            v-for="(stat, index) in PokemonDetail.stats"
            v-bind:key="`second-${index}`"
          >
			<td>{{stat.jap}}</td>
			<td>{{stat.base_stat}}</td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Pokemon',
  data() {
    return {
      pending: true,
      error: false,
      PokemonDetail: {},
      PokemonSpecies: {},
      jaTypes: [],
      pokemonTypes: {
        "normal": "ノーマル",
        "fire": "ほのお",
        "water": "みず",
        "electric": "でんき",
        "grass": "くさ",
        "ice": "こおり",
        "fighting": "かくとう",
        "poison": "どく",
        "ground": "じめん",
        "flying": "ひこう",
        "psychic": "エスパー",
        "bug": "むし",
        "rock": "いわ",
        "ghost": "ゴースト",
        "dragon": "ドラゴン",
        "dark": "あく",
        "steel": "はがね",
        "fairy": "フェアリー",
      },
      pokemonStats:["HP", "攻撃", "防御", "特攻", "特防", "素早"]
    };
  },
    mounted() {
    this.requestData();
  },
  methods: {
    async requestData() {
      this.pending = true;
      try {
		const res = await axios.get("https://pokeapi.co/api/v2/pokemon/" + this.$route.params.id);
		this.PokemonDetail = res.data;
		for (let key in this.pokemonTypes){
          for (let i in this.PokemonDetail.types) {
            if (key == this.PokemonDetail.types[i].type.name)
              this.jaTypes.push(this.pokemonTypes[key]);
          }
        }
		for (let i in this.pokemonStats){
          this.PokemonDetail.stats[i]['jap'] = this.pokemonStats[i];
		}
        this.error = false;
      } catch (e) {
        this.error = e;
      }
      try {
        const res = await axios.get("https://pokeapi.co/api/v2/pokemon-species/" + this.$route.params.id);
        this.PokemonSpecies = res.data;
      } catch (e) {
        this.error = e;
      }
      this.pending = false;
    },
	handleBack() {
		this.$router.back();
	}
  },
  render() {
    return this.$scopedSlots.default({
      pending: this.pending,
      error: this.error,
      data: this.data
    });
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Lato&family=Rubik:wght@500&display=swap');
h2 {
  font-family: 'Rubik', sans-serif;
  color: red;
}
table {
  border-collapse: collapse;
  border: solid 2px grey;
  margin: auto;
}
table tr {
	background: white;
}
table td {
  padding: 7px 20px;
  font-family: 'Lato', sans-serif;
}
.return {
	color: white;
}
.pokemon {
  text-align: center;
}
.card-img {
  background-color: white;
  width: 150px;
  height: 150px;
  margin: 10px;
  border-radius: 20%;
}
.card-body {
	text-align: center;
}
.description {
	padding-top: 30px;
	font-size: 20px;
	font-weight: bold;
}
</style>
