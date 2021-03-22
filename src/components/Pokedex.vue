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
      <div id="loader" class="loader" v-if="isLoading">
        <div class="circle"></div>
        <div class="circle"></div>
        <div class="circle"></div>
      </div>
      <div class="footer">Icons made by <a href="" title="Those Icons">Those Icons</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a></div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Pokedex',
  data() {
    return {
      pending: true,
      error: false,
      isLoading: false,
      CurrentPokemons: [],
      PokemonList: [],
      filteredPokemons:[],
      keyword: '',
      type: '',
      offset: 0,
      limit: 30
    };
  },
  created() {
    this.requestData();
    window.addEventListener('scroll', this.onScroll)
  },
  methods: {
    async requestData() {
      if (this.isLoading == false){
          this.isLoading = true;
      try {
		const res = await axios.get(`https://pokeapi.co/api/v2/pokemon?offset=${this.offset}&limit=${this.limit}`)
		this.CurrentPokemons = res.data.results;
        this.error = false;
      } catch (e) {
        this.error = e;
      }
      try {
        for (const result of this.CurrentPokemons)
        {
          const detailinfo = await axios.get(result.url);
          result["id"] = detailinfo.data.id;
          const typelist = [];
          for (let i in detailinfo.data.types){
            typelist.push(detailinfo.data.types[i].type.name);
          }
          result["type"] = typelist;
          this.PokemonList.push(result);
        }
		if (this.keyword) {
          this.filterByKeyword();
		}
		else if (this.type) {
          this.addTypeData();
		} else {
          this.filteredPokemons = this.PokemonList;
		}
		this.isLoading = false;
      } catch(e) {
          this.error = e;
      }
      this.pending = false;
      }
	},
	addTypeData() {
      for (let i in this.CurrentPokemons){
        for (let j in this.CurrentPokemons[i].type){
            if (this.type == this.CurrentPokemons[i].type[j]){
              this.filteredPokemons.push(this.CurrentPokemons[i]);
            }
          }
        }
	},
	addKeywordData() {
     for (let i in this.this.CurrentPokemons) {
          let select = this.CurrentPokemons[i];
          if(select.name.indexOf(this.keyword) !== -1) {
            this.filteredPokemons.push(select);
          }
      }
	},
	onScroll:function() {
      const { scrollTop, scrollHeight, clientHeight} = document.documentElement;
      if (scrollTop + clientHeight >= scrollHeight - 5) {
        if (this.isLoading == false && this.offset <= 810){
          this.offset += 30;
          this.requestData();
        } else if (this.isLoading == false && this.offset == 840){
          this.limit = 28;
          this.offset = 870;
          this.requestData();
        }

      }
	},
	removeFilter() {
      this.keyword = '';
      this.type = '';
      this.filteredPokemons = this.PokemonList;
	},
	filterByKeyword() {
      this.type = '';
      this.filteredPokemons = [];
      for(let i in this.PokemonList) {
          let select = this.PokemonList[i];
          if(select.name.indexOf(this.keyword) !== -1) {
            this.filteredPokemons.push(select);
          }
      }
	},
    filterByType(value) {
      this.keyword = '';
      this.filteredPokemons = [];
      if (value === "all"){
		this.type = '';
        this.filteredPokemons = this.PokemonList;
      } else {
        this.type = value;
        for (let i in this.PokemonList){
          for (let j in this.PokemonList[i].type){
            if (value === this.PokemonList[i].type[j]){
              this.filteredPokemons.push(this.PokemonList[i]);
            }
          }
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
	padding: 0px 50px 50px 50px;
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
.loader {
	opacity: 1;
	display: flex;
	position: fixed;
	left: 50%;
	bottom: 30px;
	transition: opacity 0.3s ease-in;
}
/* .loader.show {
	opacity: 1;
} */
.circle {
	display: inline-block;
	background-color : red;
	width: 10px;
	height: 10px;
	border-radius: 50%;
	margin: 5px;
	animation: bounce 0.5s ease-in infinite;
}
.circle:nth-of-type(2) {
	animation-delay: 0.1s;

}
.circle:nth-of-type(3) {
	animation-delay: 0.2s;
}
@keyframes bounce {
	0%,
	100% {
		transform: translateY(0);
	}
	50% {
		transform: translateY(-10px);
	}
}
.footer {
	text-align: center;
}
</style>
