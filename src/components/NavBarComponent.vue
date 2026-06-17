<template>
  <div>
    <nav id="nav">
      <router-link to="/" id="logo-url">
        <img src="/img/logo_acai.svg" id="logo" alt="Logo Açaí Tropical" />
        <span id="marca">Açaí <strong>Tropical</strong></span>
      </router-link>
      <div id="links">
        <router-link to="/">Início</router-link>
        <router-link to="/menu">Cardápio</router-link>
        <router-link to="/pedidos">Pedidos</router-link>
      </div>
    </nav>
  </div>
</template>
<script>
export default {
  name: "NavBarComponent",
};
</script>
<style scoped>
#nav {
  background-color: #2e1140;
  border-bottom: #7cc04a 4px solid;
  padding: 14px 50px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

#logo-url {
  display: flex;
  align-items: center;
  gap: 12px;
  text-decoration: none;
}

#logo {
  width: 52px;
  height: 52px;
  transition: 0.3s;
}
#logo:hover {
  transform: rotate(360deg) scale(1.1);
}

#marca {
  color: #fff;
  font-size: 24px;
  letter-spacing: 0.5px;
}
#marca strong {
  color: #7cc04a;
}

#links {
  display: flex;
  align-items: center;
  gap: 28px;
}

#nav a {
  color: #e7d5f5;
  text-decoration: none;
  font-size: 18px;
  font-weight: bold;
  transition: 0.2s;
}

#links a:hover {
  color: #7cc04a;
}

#links a.router-link-exact-active {
  color: #fff;
  border-bottom: 2px solid #7cc04a;
  padding-bottom: 2px;
}

@media (max-width: 600px) {
  #nav {
    flex-direction: column;
    gap: 14px;
    padding: 14px 20px;
  }
  #links {
    gap: 18px;
  }
}
</style>
