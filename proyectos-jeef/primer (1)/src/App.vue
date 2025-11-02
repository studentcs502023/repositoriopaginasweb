
<template>
  <div>
    <input
      type="text"
      v-model="apicode"
      placeholder="Escribe un nombre de Pokémon"
    />



    <button @click="buscarPokemon">Buscar</button>

    <div v-if="pokemon && pokemon.sprites" class="container">



      <div class="imag">
        <h2>{{ pokemon.name }}</h2>
        <img :src="pokemon.sprites.front_default" alt="pokemon" />
        <img :src="pokemon.sprites.other.showdown.front_default" alt="pokemon" />

        <h2>Peso: {{ pokemon.weight }}</h2>
        <h2>Altura: {{ pokemon.height }}</h2>
      </div>
   

    <div v-if="pokemon" >
      <h2>#{{ pokemon.id }}</h2>
<h2
  v-for="(it, i) in pokemon.types"
  :key="i"
  :style="color(it.type.name)"
>
{{ it.type.name }}
</h2>


      <div v-if="weak.length > 0"  >
        <h3>Debilidades:</h3>
        <ul>
          <li  v-for="(d, i) in weak" :key="i" :style="color(d.name)">
            {{ d.name }}
          </li>
        </ul>
      </div>

    </div>


      <div v-if="pokemon.stats">
        <h2 v-for="(it, i) in pokemon.stats" :key="i" class="stat" >
          Estadística: {{ it.stat.name }} <br />
          Valor: {{ it.base_stat }}
        </h2>
      </div>


    <div v-if="error" style="color: red">{{ error }}</div>
  </div>


  <div v-if="pokeball"  :class="{activo : pokeball}"  class="pokeball" >
    <div class="line"></div>
    <div class="button"></div>
  </div>
</div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";


const tipoColor = {
  normal: "#A8A77A",
  fire: "#EE8130",
  water: "#6390F0",
  electric: "#F7D02C",
  grass: "#7AC74C",
  ice: "#96D9D6",
  fighting: "#C22E28",
  poison: "#A33EA1",
  ground: "#E2BF65",
  flying: "#A98FF3",
  psychic: "#F95587",
  bug: "#A6B91A",
  rock: "#B6A136",
  ghost: "#735797",
  dragon: "#6F35FC",
  dark: "#705746",
  steel: "#B7B7CE",
  fairy: "#D685AD"
};


const pokeball = ref(false)
const apicode = ref("");
const pokemon = ref(null);
const error = ref("");
const url = ref(null);
const weak = ref([]);

async function buscarPokemon() {
  try {
    error.value = "";
    pokemon.value = null;
    weak.value = [];

    if (!apicode.value) {
      error.value = "Por favor ingresa el nombre de un Pokémon";
      return;
    }

    // Obtener Pokémon
    const { data } = await axios.get(
      `https://pokeapi.co/api/v2/pokemon/${apicode.value.toLowerCase()}`
    );
    pokemon.value = data;

    // Obtener tipo
    url.value = pokemon.value.types[0].type.url;

    // Obtener debilidades
    const resWeak = await axios.get(url.value);
    weak.value = resWeak.data.damage_relations.double_damage_from;
 pokeball.value = true

  } catch (err) {
    error.value = "Pokémon no encontrado o error en la petición";
    console.error(err);
  }
}

function cambiarColor(types) {
  // Si no hay tipos
  if (!types || types.length === 0) return {};

  // Si hay más de un tipo (degradado)
  if (types.length > 1) {
    return {
      background: `linear-gradient(135deg,
        ${tipoColor[types[0].type.name]},
        ${tipoColor[types[1].type.name]})`,
      color: "white",
      padding: "6px 12px",
      borderRadius: "10px",
      textShadow: "1px 1px 3px black",
    };
  }

  // Si hay solo un tipo (color sólido)
  return {
    backgroundColor: tipoColor[types[0].type.name],
    color: "white",
    padding: "6px 12px",
    borderRadius: "10px",
    textShadow: "1px 1px 3px black",
  };
}
function color(typeName) {
  const colorBase = tipoColor[typeName];

  return {
    backgroundColor: colorBase ? colorBase : "#888", // color gris si no existe
    color: "white",
    padding: "6px 12px",
    borderRadius: "10px",
    textShadow: "1px 1px 3px black",
    margin: "4px",
    display: "inline-block"
  };
}

</script>


<style>
.container{
display: grid;
grid-template-columns: repeat(3, 1fr);

}

input {
  padding: 8px;
  margin-right: 8px;
}
button {
  padding: 8px 12px;
  cursor: pointer;
}


:root {
      --red: #ff1c1c;
      --white: #fff;
      --black: #000;
    }

  

    .pokeball {
      position: relative;
      width: 200px;
      height: 200px;
      border-radius: 50%;
      background: var(--white);
      overflow: hidden;
      box-shadow: 0 0 40px rgba(0, 0, 0, 0.8);
      cursor: pointer;
      transition: transform 0.3s ease;
    }

    .pokeball::before {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 50%;
      background: var(--red);
      border-top-left-radius: 50%;
      border-top-right-radius: 50%;
      transition: transform 0.6s ease;
      transform-origin: bottom;
    }

    .pokeball::after {
      content: "";
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      height: 50%;
      background: var(--white);
      border-bottom-left-radius: 50%;
      border-bottom-right-radius: 50%;
    }

    .line {
      position: absolute;
      top: 50%;
      left: 0;
      width: 100%;
      height: 20px;
      background: var(--black);
      transform: translateY(-50%);
      z-index: 2;
    }

    .button {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 60px;
      height: 60px;
      border-radius: 50%;
      background: var(--white);
      border: 8px solid var(--black);
      z-index: 3;
      box-shadow: 0 0 0 4px var(--white), 0 0 25px rgba(255, 255, 255, 0);
      transition: box-shadow 0.4s ease;
    }

    .pokeball.open::before {
      transform: rotateX(160deg);
    }

    .pokeball.open .button {
      box-shadow: 0 0 0 4px var(--white), 0 0 45px 15px rgba(255, 255, 255, 0.9);
    }


</style>


