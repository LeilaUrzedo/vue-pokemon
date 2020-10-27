<template>
  <div id="app">
    <header><h1 class="title-chamada">Pokémon</h1></header>
    <Main></Main>
  </div>
</template>

<script>
import Main from './components/Main.vue'

export default {
  name: 'App',
  components: {
    Main
  }
}
</script>

<style src="./assets/scss/global.scss" lang="scss" />