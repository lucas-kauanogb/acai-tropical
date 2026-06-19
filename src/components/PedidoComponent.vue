<template>
  <div>
    <div ref="topoAlerta"></div>
    <AlertaComponent
      :tipo="alerta.tipo"
      :mensagem="alerta.mensagem"
      :visivel="alerta.visivel"
    />
    <form id="pedido-form" @submit="criarPedido($event)">
      <div>
        <p id="nome-acai-content">
          {{ acai && acai.nome ? acai.nome : "-" }}
        </p>
        <img id="foto-content" :src="acai && acai.foto ? acai.foto : ''" />
      </div>
      <div class="inputs" id="form-pedido">
        <label for="nome-cliente">Nome do cliente</label>
        <input
          type="text"
          v-model="nomeCliente"
          id="nome-cliente"
          name="nome-cliente"
          placeholder="Digite seu nome"
        />
      </div>
      <div class="inputs">
        <label>Tamanho da tigela</label>
        <select name="tamanho" id="tamanho" v-model="tamanhoSelecionado">
          <option value="" selected>Selecione o tamanho</option>
          <option
            v-for="tamanho in listaTamanhos"
            :key="tamanho.id"
            :value="tamanho"
          >
            {{ tamanho.descricao }}
            <template v-if="tamanho.valor_extra">(+ R$ {{ tamanho.valor_extra }})</template>
          </option>
        </select>
      </div>
      <div class="inputs">
        <label id="opcionais-titulo"> Personalize seu açaí</label>
        <label id="opcionais-subtitulo"> Adicione complementos</label>

        <div
          class="checkbox-container"
          v-for="complemento in listaComplementos"
          :key="complemento.id"
        >
          <input
            type="checkbox"
            :name="complemento.nome"
            :value="complemento"
            v-model="listaComplementosSelecionados"
          />
          <span>{{ complemento.nome }} — R$ {{ complemento.valor }}</span>
        </div>

        <label> Adicione uma bebida</label>

        <div
          class="checkbox-container"
          id="checkbox-container"
          v-for="bebida in listaBebidas"
          :key="bebida.id"
        >
          <input
            type="checkbox"
            :name="bebida.nome"
            :value="bebida"
            v-model="listaBebidasSelecionadas"
          />
          <span>{{ bebida.nome }} — R$ {{ bebida.valor }}</span>
        </div>
        <div class="inputs">
          <input type="submit" class="submit-btn" value="Confirmar Pedido" />
        </div>
      </div>
    </form>
  </div>
</template>
<script>
import AlertaComponent from "@/components/AlertaComponent.vue";

export default {
  name: "PedidoComponent",
  components: {
    AlertaComponent,
  },
  props: {
    acai: null,
  },
  data() {
    return {
      listaTamanhos: [],
      listaComplementos: [],
      listaBebidas: [],
      nomeCliente: "",
      tamanhoSelecionado: "",
      listaComplementosSelecionados: [],
      listaBebidasSelecionadas: [],
      confirmacaoPendente: false,
      alerta: {
        visivel: false,
        tipo: "info",
        mensagem: "",
      },
    };
  },
  methods: {
    mostrarAlerta(tipo, mensagem) {
      this.alerta = { visivel: true, tipo, mensagem };
      this.$nextTick(() => {
        if (this.$refs.topoAlerta) {
          this.$refs.topoAlerta.scrollIntoView({
            behavior: "smooth",
            block: "center",
          });
        }
      });
    },
    async getTamanhos() {
      const response = await fetch(`${this.$apiUrl}/tamanhos`);
      const dados = await response.json();
      this.listaTamanhos = dados;
    },
    async getOpcionais() {
      const response = await fetch(`${this.$apiUrl}/opcionais`);
      const dados = await response.json();
      this.listaComplementos = dados.complemento;
      this.listaBebidas = dados.bebidas;
    },
    validarPedido() {
      if (!this.acai || !this.acai.nome) {
        this.mostrarAlerta(
          "aviso",
          "Volte ao Cardápio e selecione um açaí antes de continuar."
        );
        return false;
      }
      if (this.nomeCliente.trim() === "") {
        this.mostrarAlerta("erro", "Informe o nome do cliente para continuar.");
        return false;
      }
      if (this.tamanhoSelecionado === "") {
        this.mostrarAlerta("erro", "Selecione o tamanho da tigela.");
        return false;
      }
      return true;
    },
    async criarPedido(e) {
      e.preventDefault();

      if (!this.validarPedido()) {
        return;
      }

      
      const semExtras =
        this.listaComplementosSelecionados.length === 0 &&
        this.listaBebidasSelecionadas.length === 0;
      if (semExtras && !this.confirmacaoPendente) {
        this.confirmacaoPendente = true;
        this.mostrarAlerta(
          "aviso",
          "Seu açaí está sem complementos nem bebidas. Toque em Confirmar novamente para enviar assim mesmo."
        );
        return;
      }

      const dadosPedido = {
        nome: this.nomeCliente,
        tamanho: this.tamanhoSelecionado,
        complemento: Array.from(this.listaComplementosSelecionados),
        bebidas: Array.from(this.listaBebidasSelecionadas),
        acai: this.acai,
        statusId: 1,
      };

      const dadosJson = JSON.stringify(dadosPedido);

      try {
        const req = await fetch(`${this.$apiUrl}/pedidos`, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: dadosJson,
        });

        if (!req.ok) {
          throw new Error("Falha ao registrar o pedido");
        }

        this.mostrarAlerta("sucesso", "Pedido confirmado com sucesso! Redirecionando...");
        setTimeout(() => {
          this.$router.push("/pedidos");
        }, 1200);
      } catch (erro) {
        this.mostrarAlerta(
          "erro",
          "Não foi possível registrar o pedido. Tente novamente."
        );
      }
    },
  },
  mounted() {
    this.getTamanhos();
    this.getOpcionais();
  },
};
</script>
<style scoped>
#foto-content {
  margin-bottom: 16px;
  border-radius: 16px;
  justify-content: center;
  width: 100%;
  height: 200px;
  object-fit: cover;
}

#nome-acai-content {
  font-size: 40px;
  font-weight: bold;
  text-align: start;
  margin-bottom: -80px;
  margin-left: 24px;
  color: #fff;
  padding: 16px;
  text-shadow: 0 2px 6px rgba(0, 0, 0, 0.5);
  position: relative;
  z-index: 1;
}

#pedido-form {
  max-width: 750px;
  margin: 0 auto;
}

#form-pedido {
  max-width: 750px;
}

.inputs {
  display: flex;
  flex-direction: column;
  margin-bottom: 16px;
}

label {
  font-weight: bold;
  margin-bottom: 12px;
  color: #2b1b33;
  padding: 5px 12px;
  display: flex;
  border-left: 4px solid #7cc04a;
}

input,
select {
  padding: 12px;
  width: 300px;
  border: solid #c9b3dd 1px;
  border-radius: 8px;
  height: 44px;
  font-size: 14px;
}

#opcionais-titulo {
  width: 100%;
}

#opcionais-subtitulo {
  display: flex;
  align-items: flex-start;
  width: 100%;
  margin-bottom: 12px;
}

.checkbox-container {
  display: flex;
  align-items: center;
  margin-bottom: 8px;
}

.checkbox-container span {
  margin-left: 8px;
  font-weight: bold;
}

.checkbox-container input {
  width: auto;
  height: 18px;
}

.submit-btn {
  background-color: #5b1d8a;
  color: #fff;
  font-weight: bold;
  border: none;
  font-size: 18px;
  border-radius: 30px;
  padding: 16px;
  margin: 10px auto 0 auto;
  cursor: pointer;
  width: 100%;
  height: auto;
  transition: 0.3s;
}

.submit-btn:hover {
  background-color: #7cc04a;
  color: #2e1140;
}
</style>
