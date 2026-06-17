<template>
  <div>
    <h1>Nosso Cardápio</h1>

    <AlertaComponent
      tipo="info"
      :mensagem="alerta.mensagem"
      :visivel="alerta.visivel"
    />

    <p id="subtitulo">Escolha sua base de açaí e personalize na próxima etapa 👇</p>

    <div id="scroll-horizontal">
      <div id="card-content" v-for="acai in listaMenuAcais" :key="acai.id">
        <div id="card-linha">
          <div class="foto-acai">
            <span v-if="acai.eh_novidade" class="selo-novidade">Novidade</span>
            <img :src="acai.foto" :alt="acai.nome" />
            <div class="card-coluna">
              <p id="nome-content">{{ acai.nome }}</p>
              <p id="preco-content">a partir de R$ {{ acai.valor }},00</p>
              <p id="descricao-content">{{ acai.descricao }}</p>
              <button @click="selecionarAcai(acai)">Montar meu açaí</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import AlertaComponent from "@/components/AlertaComponent.vue";

export default {
  name: "MenuView",
  components: {
    AlertaComponent,
  },
  data() {
    return {
      listaMenuAcais: [],
      alerta: {
        visivel: false,
        mensagem: "",
      },
    };
  },
  methods: {
    async consultarMenu() {
      const response = await fetch(`${this.$apiUrl}/menu`);
      const dados = await response.json();
      this.listaMenuAcais = dados.acais;
    },
    selecionarAcai(acaiSelecionado) {
      const param = JSON.stringify(acaiSelecionado);
      const acaiJson = encodeURIComponent(param);
      this.$router.push({ path: "/config", query: { acai: acaiJson } });
    },
  },
  mounted() {
    this.consultarMenu();
    this.alerta = {
      visivel: true,
      mensagem: "Bem-vindo(a)! Deslize para ver os sabores e clique para personalizar.",
    };
  },
};
</script>
<style scoped>
#subtitulo {
  color: #6a4c93;
  margin-bottom: 18px;
  font-size: 16px;
}

#card-content {
  display: inline-block;
  vertical-align: top;
  width: 280px;
  min-height: 500px;
  margin: 20px;
  border: 1px solid #eadcf5;
  border-radius: 18px;
  box-shadow: 0 6px 18px rgba(58, 20, 87, 0.12);
  position: relative;
  overflow: hidden;
  background: #fff;
  transition: 0.3s;
}
#card-content:hover {
  transform: translateY(-6px);
  box-shadow: 0 12px 26px rgba(58, 20, 87, 0.2);
}

#scroll-horizontal {
  overflow-x: auto;
  white-space: nowrap;
  max-width: 960px;
  margin: 0 auto;
  padding-bottom: 10px;
}

.foto-acai {
  position: relative;
}

.foto-acai img {
  width: 100%;
  object-fit: cover;
  max-height: 200px;
  border-radius: 18px 18px 0 0;
}

.selo-novidade {
  position: absolute;
  top: 12px;
  left: 12px;
  background: #7cc04a;
  color: #2e1140;
  font-weight: bold;
  font-size: 12px;
  padding: 4px 12px;
  border-radius: 20px;
  z-index: 2;
}

.card-coluna {
  display: flex;
  flex-direction: column;
}

#nome-content {
  font-size: 24px;
  font-weight: bold;
  text-align: center;
  width: 100%;
  margin: 12px 0 4px 0;
  color: #3a1457;
  white-space: normal;
}

#preco-content {
  font-size: 22px;
  text-align: center;
  font-weight: bold;
  color: #4e8a2c;
  margin: 6px 16px;
}

#descricao-content {
  font-size: 15px;
  text-align: left;
  color: gray;
  margin: 12px 16px;
  white-space: normal;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 5;
  -webkit-box-orient: vertical;
}

.card-coluna button {
  margin: 18px auto 22px auto;
  padding: 12px;
  background-color: #5b1d8a;
  color: #fff;
  font-weight: bold;
  border-radius: 30px;
  border: none;
  font-size: 15px;
  width: 80%;
  cursor: pointer;
  transition: 0.3s;
}
.card-coluna button:hover {
  background-color: #7cc04a;
  color: #2e1140;
}
</style>
