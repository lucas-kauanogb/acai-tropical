<template>
  <div>
    <h1>Monte seu Pedido</h1>
    <pedido-component-vue :acai="this.acaiSelecionado" />
  </div>
</template>
<script>
import PedidoComponentVue from "@/components/PedidoComponent.vue";
export default {
  name: "ConfiguracaoPedidoView",
  components: {
    PedidoComponentVue,
  },
  data() {
    return {
      acaiSelecionado: null,
    };
  },
  mounted() {
    const query = this.$route.query;
    if (query.acai) {
      const decodeAcai = JSON.parse(decodeURIComponent(query.acai));
      this.acaiSelecionado = decodeAcai;
    }
  },
};
</script>
<style scoped></style>
