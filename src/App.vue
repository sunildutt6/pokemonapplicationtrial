<template>
  <div class ="wrapper">
  <div class="container">

  <nav class="navbar navbar-expand-lg bg-body-tertiary">
  <div class="container-fluid">
    <img alt="Poke logo" src="./assets/logo.png" style="width: 150px"/>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
  </div>
</nav>

<!--Search fields-->
<input type="text" placeholder="Search your poke..." v-model="search" />

<!--display Card-->
<div class="row mt-3" >
  <div class="col mt-3" v-for="pokemon in filtered_pokemons" :key="pokemon.name">
    <div class="card" style="width:18rem">
      <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_id(pokemon)}.png`"  :alt="pokemon.name"  class="card-image"  />
      <h4 class="text-center mt-3">{{ get_name(pokemon) }}</h4>
      <button class="btn btn-secondary">Explore</button>
    </div>
  </div>
</div>
   
  </div>
  </div>



<!--Footer area-->
<p className="footer">Pokemon application 2023</p>
<!--footer close-->
</template>

<script>
import axios from "axios";
export default {
  name: "App",

  components: {},

  data() {
    return {
      pokemons: [],
      search: "",
      show_dialog: false,
      selected_pokemon: null,
    };
  },

  mounted() {
    axios
      .get("https://pokeapi.co/api/v2/pokemon?limit=30")
      .then((response) => {
        this.pokemons = response.data.results;
      });
  },
  methods: {
    get_id(pokemon) {
      return Number(pokemon.url.split("/")[6]);
    },
    get_name(pokemon) {
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
    },
    show_pokemon(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((response) => {
        this.selected_pokemon = response.data;
        this.show_dialog = !this.show_dialog;
      });
    },
    get_move_level(move) {
      for (let version of move.version_group_details) {
        if (
          version.version_group.name == "sword-shield" &&
          version.move_learn_method.name == "level-up"
        ) {
          return version.level_learned_at;
        }
      }
      return 0;
    },
    filter_moves(pokemon) {
      return pokemon.moves.filter((item) => {
        let include = false;
        for (let version of item.version_group_details) {
          if (
            version.version_group.name == "sword-shield" &&
            version.move_learn_method.name == "level-up"
          ) {
            include = true;
          }
        }
        return include;
      });
    },
  },
  computed: {
    filtered_pokemons() {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search);
      });
    },
  },
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto&family=Twinkle+Star&display=swap');
:root{
    --primary:#F7F5F7;
    --teritary:#97D4E6;
    --secondary:#F4E15E;
    --red:#B62D3D;
    --black:#3B4C68;
}
*{
    box-sizing:border-box;
    padding:0;
    margin:0;
}
html{
    font-size: 100%;
}
html,body{
    height:100%;
}
body{
    font-family: 'Roboto', sans-serif;
    background-color: var(--teritary) !important;
    font-size:1.12rem;
}

#app{
    height:100%;
    display:flex;
    flex-direction:column;
    
}

.wrapper{
    flex: 1 0 auto;
}

h1,h2,h3,h5,h6{
    font-family: 'Twinkle Star', cursive;
    color: var(--black);
    margin: 1rem 0;
     
}
img{
  width:150px;
}
.card{
  box-shadow: 0 5px 5px 0 var(--secondary);
}
.card-image{
  display: block;
  margin:0 auto;
}

input{
  padding: 5px 5px;
  border-radius: 5px;
  outline: none;
  width: 450px;
}

.footer{
    flex-shrink:0;
    display: flex;
    justify-content:center;
    margin:0;
    padding:0.5rem;
    background-color: var(--secondary);
    font-weight: 500;
}

</style>
