<template>
    <h1>Kanto Dex</h1>
    <div class="body">
      <div class="selPokemon" v-if="selPokemon">
        <div class="selPokemon-images">
          <img class="sel-Image" :src="selPokemon.sprites.front_default" alt="front sprite" />
          <a v-if="favorites.includes(selPokemon.name)">
          <svg @click="removeFavorite" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"></path></svg>
          </a>
          <a v-if="!favorites.includes(selPokemon.name)">
          <svg @click="favorites.push(selPokemon.name)" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"></path></svg>
          </a>
        </div>
        <h3>{{selPokemon.name}}</h3>
        <p># {{selPokemon.id}}</p>
        <p>Height: {{selPokemon.height}}</p>
        <p>Weight: {{selPokemon.weight}}</p>
      </div>
      <div class="allPokemon">
        <div class="filter-nav">
          <button v-for="type in types" :key="type" @click="filterPokemon(type)">{{type}}</button>
          <button @click="viewFavorites">FAVORITED</button>
          <button @click="filteredPokemon = pokemon">RESET</button>
        </div>
        <ul>
          <li v-for="pokemon in filteredPokemon" :key="pokemon.name">
            <div @click="showPokemon(pokemon)"> 
              <img class="rounded-box btn bg-secondary h-24" :src="pokemon.sprites.front_default" alt="front sprite" />
              <a :href="pokemon.url">{{ pokemon.name }}</a>
            </div>
          </li>
        </ul>
      </div>
    </div>
</template>

<script>
export default{


data () {
  return {
    pokemon: [],
    filteredPokemon: [],
    selPokemon: null,
    types: [
      'bug',
      'dragon',
      'electric',
      'fighting',
      'fire',
      'flying',
      'ghost',
      'grass',
      'ground',
      'ice',
      'normal',
      'poison',
      'psychic',
      'rock',
      'water',
      'dark',
      'steel',
      'fairy'
    ],
    favorites: []
  }
},

methods: {
    async load() {
        try{
            const retrievedpokemon = await this.getPokemon();
            this.pokemon = retrievedpokemon;
            this.filteredPokemon = retrievedpokemon;
        } catch (err){
            console.error('Error retreiving pokemon', err);
        }
    },

    async getPokemon(){
        let tenPokemon = []
        for (let i = 1; i <= 151; i++){
            try{
                const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${i}/`);
                const pokemonData = await response.json();
                tenPokemon.push(pokemonData);
            } catch(err){
                console.error('Error getting pokemon', err)
            }
        }
        return tenPokemon

    },

    showPokemon(pokemon){
      console.log(pokemon);
        this.selPokemon = pokemon;
    },

    filterPokemon(type){
      this.filteredPokemon = this.pokemon.filter(pokemon => {
        if(pokemon.types.filter(e => e.type.name === type).length > 0)
        {
          return true
        }
      });
    },

    viewFavorites(){
      this.filteredPokemon = this.pokemon.filter(pokemon => {
        if(this.favorites.includes(pokemon.name))
        {
          return true
        }
      });
    },

    removeFavorite(){
      this.favorites = this.favorites.filter(favorite => favorite !== this.selPokemon.name);
      // this.viewFavorites();
    }
},

mounted(){
    this.load();
}
  
  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.body {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  align-items: center;
  flex-wrap: wrap;
}

h3 {
  margin: 40px 0 0;
}

.selPokemon {
  padding: 1em;
  margin: 1em;
  border: 1px solid black;
  border-radius: 10px;
}

.selPokemon div {
  border-bottom: 1px solid black;
}

.allPokemon{
  width: 100%;
}

.filter-nav {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  background-color: #dddddd;
  padding: 1em 0;
  justify-content: space-around;
}

.sel-Image {
  width: 200px;
  height: auto;
}

.selPokemon-images {
  display: flex;
  flex-direction: row;
  align-self: center;
}

svg {
  width: 2em;
  stroke: black;
}

button {
  margin: 0 10px;
  padding: 1em;
  border: 1px solid #42b983;
  border-radius: 10px;
  background-color: #42b983;
  color: white;
  cursor: pointer;
}

ul {
  list-style-type: none;
  padding: 0;
  display: flex;
  flex-wrap: wrap;
}
li {
  margin: 0 10px;
}
li div {
  display: flex;
  flex-direction: column;
  padding: 1em;
  border: 1px solid #42b983;
  margin: .5em;
  border-radius: 10px;
}

li div:hover {
  background-color: #42b983;
}

a {
  color: black;
}
</style>
