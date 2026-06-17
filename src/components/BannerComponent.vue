<template>
  <div id="main-banner">
    <img id="banner-img" src="/img/banner.svg" alt="banner Açaí Tropical" />
    <div id="banner-texto">
      <h1>O açaí mais cremoso da cidade 🍇</h1>
      <p>Monte sua tigela do seu jeito: escolha o tamanho e capriche nos complementos.</p>
      <router-link to="/menu" id="banner-btn">Ver cardápio</router-link>
    </div>
  </div>
</template>
<script>
export default {
  name: "BannerComponent",
};
</script>
<style scoped>
#main-banner {
  position: relative;
  height: 320px;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  overflow: hidden;
}
#banner-img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: 0;
}
#banner-texto {
  position: relative;
  z-index: 1;
  max-width: 520px;
  margin-left: 7%;
  text-align: left;
  color: #fff;
}
#banner-texto h1 {
  font-size: 38px;
  margin-bottom: 12px;
  text-shadow: 0 2px 8px rgba(0, 0, 0, 0.4);
}
#banner-texto p {
  font-size: 17px;
  line-height: 1.5;
  margin-bottom: 22px;
  text-shadow: 0 1px 4px rgba(0, 0, 0, 0.4);
}
#banner-btn {
  display: inline-block;
  background-color: #7cc04a;
  color: #2e1140;
  font-weight: bold;
  text-decoration: none;
  padding: 12px 28px;
  border-radius: 30px;
  font-size: 16px;
  transition: 0.3s;
}
#banner-btn:hover {
  background-color: #fff;
  transform: translateY(-2px);
}
@media (max-width: 600px) {
  #banner-texto {
    margin: 0 20px;
  }
  #banner-texto h1 {
    font-size: 28px;
  }
}
</style>
