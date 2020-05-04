<template>
  <div id="app">
    <div class="hero is-white is-gradient is-bold">
      <div class="hero-body">
        <h1>
          <span class="has-text-success">R&M</span>
          <span class="subtitle">Personajes</span>
        </h1>
        <div class="field has-addons is-pulled-right">
          <div class="control">
            <input
              v-model="search"
              type="text"
              class="input is-rounded"
              v-on:keyup.enter="searchData"
            >
          </div>
          <div class="control">
            <button class="button is-success is-rounded" v-on:click="searchData">Consultar</button>
          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="columns is-desktop is-mobile is-tablet is-multiline is-centered">
        <Character
          @showModal="showModal"
          v-for="character of characters"
          :character="character"
          :key="character.id"
        />
      </div>

      <nav class="pagination" roles="navegation" aria-label="pagination">
        <a class="pagination-previous" v-on:click="ChangePage(page-1);">Anterior</a>
        <ul class="pagination-list">
          <a class="panination-link is-current">{{page}}</a>
        </ul>
        <a class="pagination-next" v-on:click="ChangePage(page+1);">Siguiente</a>
      </nav>
    </div>
    <div class="modal" :class="{'is-active': modal}" v-if="modal">
      <div class="modal-background" @click="modal = false"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <p class="modal-card-title">Acerca de : {{ currentCaracter.name}}</p>
        </header>
        <section class="modal-card-body">
          <img :src="currentCaracter.image" />
          <p>Species: {{currentCaracter.species}}</p>
        </section>
        <footer class="modal-card-foot">
          <button class="button" @click="modal=false">Cancel</button>
        </footer>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Character from "./components/Character";

export default {
  name: "App",
  components: {
    Character
  },
  data: function() {
    return {
      characters: [],
      page: 1,
      pages: 1,
      search: "",
      modal: false,
      currentCaracter: {}
    };
  },
  created() {
    console.log("creando", this.fetch());
    this.fetch();
  },
  methods: {
    fetch() {
      const params = {
        page: this.page,
        name: this.search
      };
      let result = axios
        .get("https://rickandmortyapi.com/api/character/", { params })
        .then(res => {
          this.characters = res.data.results;
          this.pages = res.data.info.pages;
        })
        .catch(err => {
          console.log(err);
        });
      //return result;
    },
    ChangePage(page) {
      this.page = page <= 0 || page > this.pages ? this.page : page;
      console.log(this.page);
      this.fetch();
    },
    searchData() {
      this.page = 1;

      this.fetch();
    },
    showModal(id) {
      this.fetchOne(id);
    },
    async fetchOne(id) {
      let result = await axios.get(
        `https://rickandmortyapi.com/api/character/${id}`
      );
      this.currentCaracter = result.data;
      console.log(result.data);
      this.modal = true;
    }
  }
};
</script>

<style>
</style>
