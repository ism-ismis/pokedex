<template v-slot:default="{ pending, error, data }">
<!-- Request Pending -->
  <div>
    <div v-if="pending" class="text-center">
      Loading ...
    </div>
<!-- Request Error -->
    <div v-else-if="error" role="alert">
      {{ error }}
    </div>
<!-- Request Success -->
    <div v-else>
      <div class="header" @click="removeFilter">
          <h1 class="title"><img class="pokeball" src="/pokeball.png" alt="pokeball"> POKEDEX</h1>
      </div>
      <div class="search-box">
        <div class="search">
          <label>Search by:</label>
          <input class="search_input" type="text" v-model="keyword" placeholder="name">
          <i class="fas fa-search" @click.prevent="filterByKeyword"></i>
          <i class="fas fa-sync reverse" @click.prevent="reverse"></i>
        </div>
      </div>
      <div class="type-filter">
        <div class="type-pack">
          <div class="type-name">All</div>
          <i class="type-icon fas fa-3x fa-globe" v-on:click="filterByType('all')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Normal</div>
          <i class="type-icon fas fa-3x fa-dot-circle" v-on:click="filterByType('normal')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Fire</div>
          <i class="type-icon fas fa-3x fa-fire-alt" v-on:click="filterByType('fire')"></i>
        </div>
       <div class="type-pack">
          <div class="type-name">Water</div>
          <i class="type-icon fas fa-3x fa-tint" v-on:click="filterByType('water')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Electric</div>
          <i class="type-icon fas fa-3x fa-bolt" v-on:click="filterByType('electric')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Grass</div>
          <i class="type-icon fas fa-3x fa-seedling" v-on:click="filterByType('grass')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Ice</div>
          <i class="type-icon fas fa-3x fa-icicles" v-on:click="filterByType('ice')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Fighting</div>
          <i class="type-icon fas fa-3x fa-hand-rock" v-on:click="filterByType('fighting')"></i>
        </div>
       <div class="type-pack">
          <div class="type-name">Poison</div>
          <i class="type-icon fas fa-3x fa-skull-crossbones" v-on:click="filterByType('poison')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Ground</div>
          <i class="type-icon fas fa-3x fa-tree" v-on:click="filterByType('ground')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Flying</div>
          <i class="type-icon fas fa-3x fa-dove" v-on:click="filterByType('flying')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Psychic</div>
          <i class="type-icon fas fa-3x fa-eye" v-on:click="filterByType('psychic')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Bug</div>
          <i class="type-icon fas fa-3x fa-bug" v-on:click="filterByType('bug')"></i>
        </div>
       <div class="type-pack">
          <div class="type-name">Rock</div>
          <i class="type-icon fas fa-3x fa-gem" v-on:click="filterByType('rock')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Ghost</div>
          <i class="type-icon fas fa-3x fa-ghost" v-on:click="filterByType('ghost')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Dragon</div>
          <i class="type-icon fas fa-3x fa-dragon" v-on:click="filterByType('dragon')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Dark</div>
          <i class="type-icon fas fa-3x fa-moon" v-on:click="filterByType('dark')"></i>
        </div>
        <div class="type-pack">
          <div class="type-name">Steel</div>
          <i class="type-icon fas fa-3x fa-drum-steelpan" v-on:click="filterByType('steel')"></i>
        </div>
       <div class="type-pack">
          <div class="type-name">Fairy</div>
          <i class="type-icon fab fa-3x fa-fly" v-on:click="filterByType('fairy')"></i>
        </div>
      </div>

      <div class="pokemon-display row">
        <div
          v-for="(item, index) in filteredPokemons"
          v-bind:key="index"
          class="repeat col-6, col-sm-4 col-md-3 col-lg-2">
          <div class="card">
              <div class="card-body">
                <router-link :to="{name:'pokemon', params: {id: item.id}}">
                <img class="card-img-top" :src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/' + (item.id) + '.png'" />
				</router-link>
                <div class="card-title">
                  <p>No.{{(item.id)}} {{item.name.toUpperCase()}}</p>
                </div>
              </div>

          </div>
        </div>
      </div>
      <div class="footer">Icons made by <a href="" title="Those Icons">Those Icons</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a></div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
// import typeicon from './TypeIcon.vue';

export default {
  name: 'Pokedex',
//   components: {
//     typeicon
//   },
  data() {
    return {
      pending: true,
      error: false,
      PokemonList: [],
      filteredPokemons:[],
      keyword: '',
    };
  },

  mounted() {
    this.requestData();
  },
  computed: {
    Pokemons: {
      get:function() {
        let selects = [];
        for(let i in this.PokemonList) {
          let select = this.PokemonList[i];
          if(select.name.indexOf(this.keyword) !== -1) {
            selects.push(select);
          }
        }
        return selects;
      },
      set:function(value) {
		console.log(value);
		for (let i in value) {
          this.Pokemons.push(value[i]);
		}
      }
    }
  },
  methods: {
    async requestData() {
      this.pending = true;
      this.filteredPokemons =[];
      try {
		const res = await axios.get("https://pokeapi.co/api/v2/pokemon?offset=0&limit=251")
		this.PokemonList = res.data.results;
        this.error = false;
      } catch (e) {
        this.error = e;
      }
      try {
        for (const result of this.PokemonList)
        {
          const detailinfo = await axios.get(result.url);
          result["id"] = detailinfo.data.id;
          const typelist = [];
          for (let i in detailinfo.data.types){
            typelist.push(detailinfo.data.types[i].type.name);
          }
          result["type"] = typelist;
        }
		this.filteredPokemons = this.PokemonList;
      } catch(e) {
          this.error = e;
      }
      this.pending = false;
	},
	removeFilter() {
      this.filteredPokemons = this.PokemonList;
	},
	filterByKeyword() {
      let keywordPokemons = [];
      for(let i in this.PokemonList) {
          let select = this.PokemonList[i];
          if(select.name.indexOf(this.keyword) !== -1) {
            keywordPokemons.push(select);
          }
      }
      this.filteredPokemons = keywordPokemons;
	},
    filterByType(value) {
      let typedPokemons = [];
      if (value === "all"){
        this.filteredPokemons = this.PokemonList;
      } else {
        for (let i in this.PokemonList){
          for (let j in this.PokemonList[i].type){
            if (value === this.PokemonList[i].type[j]){
              typedPokemons.push(this.PokemonList[i]);
            }
          }
          this.filteredPokemons = typedPokemons;
        }
      }
    },
	reverse() {
		this.filteredPokemons = this.filteredPokemons.reverse();
	},
    render() {
      return this.$scopedSlots.default({
        pending: this.pending,
        error: this.error,
        data: this.data
      });
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Lato:wght@400;700&family=Rubik:wght@500&display=swap');
.search {
	font-family: 'Lato', sans-serif;
	font-size: 20px;
	text-align: center;
	padding-bottom: 20px;
}
.search_input {
	margin: 0 5px;
	width: 300px;
	height: 30px;
	font-size: 16px;
	border-radius: 3px;
	border: 2px solid #ddd;
}
.reverse {
	margin-left: 10px;
}
.title {
	color: red;
	font-size: 60px;
	font-family: 'Rubik', sans-serif;
	text-align: center;
	margin: 0px;
	padding: 3rem 0px;
}
.title:hover {
	color:brown;
}
.pokeball {
	width: 50px;
}
.type-filter {
	margin: 5px 25px;
	display: flex;
	flex-direction: row;
	justify-content: center;
	flex-wrap: wrap;
	text-align: center;
	margin-bottom: 20px;
}
.type-icon {
	margin: 0px 10px;
}
.type-icon:hover {
	color: white;
}
.pokemon-display {
	padding-right: 50px;
	padding-left: 50px;
	text-align: center;
}
.card {
	background-color: #f9f3f3;
	width: 180px;
	height: 160px;
	margin: auto;
	margin-bottom: 10px;
	padding: 10px;
	text-align: center;
	font-family: 'Lato', sans-serif;
	font-weight: bold;
	border-radius: 10%;
}
.card-img-top {
	width: 100px;
}
.card-img-top:hover {
	width: 120px;
}
.footer {
	text-align: center;
}
</style>
