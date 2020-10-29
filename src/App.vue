<template>
  <div id="app">
    <header>
      <nav class="navbar">
        <div class="navbar-brand">
          <router-link to="/" class="navbar-item">
            ContactsApp
          </router-link>
        </div>

        <div class="navbar-end">
          <router-link to="/add" class="navbar-item"
            >Add New Contact</router-link
          >
        </div>
      </nav>
    </header>
    <router-view />
  </div>
</template>

<script>
import Vue from 'vue'
import VueMaterial from 'vue-material'
import 'vue-material/dist/vue-material.min.css'
import 'vue-material/dist/theme/default.css'
Vue.use(VueMaterial)
export default {
  name: 'app'
}
</script>

<style>
#app {
  font-family: "Roboto";
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* background-color: #eee; */
}
.navbar {
  background: #122c57;
  color: black;
}

.navbar-item:hover {
  background-color: #9e7caf !important;
}
a {
  color: white !important;
}
/* .navbar-item,
.navbar-link {
  color: black;
}*/
</style>
