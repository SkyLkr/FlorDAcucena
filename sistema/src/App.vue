<template>
  <div id="app">
    <div id="nav" class="row justify-contents-start">
      <div class="col-md-2">
        <b-img :src="require('./assets/logt.png')" fluid />
      </div>
      <div class="nav-links" v-if="user.currentUser">
        <router-link class="btn botao" to="/">Pedidos</router-link>
        <router-link class="btn botao" to ="/relatorio">Relatório</router-link> 
        
      </div>
      <!-- <div class="justify-self-end">
        <b-button class="botao">Sair</b-button>
      </div> -->
    </div>
    <router-view/>
  </div>
</template>

<script>
import { auth } from './firebase'

export default {
  name: 'APPHome',
  data() {
    return {
      user: auth
    }
  }
}
</script>


<style>

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: #ffffff;
}
#nav {
  padding: 30px;
  background-color: #c79bbf;
  margin-bottom: 30px;
}

.logo {
  background-image: url("assets/fl.png");
  color: transparent;
}

.nav-links a {
  margin-top: 30px;
}

</style>
