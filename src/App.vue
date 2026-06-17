<template>
  <div>
    <NavBarComponent />
    <BannerComponent />
    <main id="conteudo">
      <router-view />
    </main>
    <footer id="rodape">
      <p>Açaí <strong>Tropical</strong> 🍇 — feito com Vue 3 · Projeto acadêmico</p>
    </footer>
  </div>
</template>
<script>
import NavBarComponent from "./components/NavBarComponent.vue";
import BannerComponent from "./components/BannerComponent.vue";
export default {
  name: "App",
  components: {
    NavBarComponent,
    BannerComponent,
  },
};
</script>
<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
}

#app {
  font-family: "Segoe UI", Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2b1b33;
  background-color: #fbf7fd;
  min-height: 100vh;
}

#conteudo {
  padding: 10px 16px 60px 16px;
  min-height: 50vh;
}

#conteudo h1 {
  color: #3a1457;
}

nav a.router-link-exact-active {
  color: #7cc04a;
}

#rodape {
  background-color: #2e1140;
  color: #e7d5f5;
  padding: 22px;
  font-size: 14px;
}
#rodape strong {
  color: #7cc04a;
}
</style>
