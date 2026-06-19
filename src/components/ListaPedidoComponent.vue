<template>
  <div>
     <div ref="topoAlerta"></div>
    <AlertaComponent
      :tipo="alerta.tipo"
      :mensagem="alerta.mensagem"
      :visivel="alerta.visivel"
    />
    <div id="pedidos-tabela">
      <div>
        <div id="pedidos-tabela-cabecalho">
          <div id="ordem-id">#ID</div>
          <div>Nome</div>
          <div>Açaí</div>
          <div>Tamanho</div>
          <div>Opcionais</div>
          <div>Status</div>
          <div id="div-acoes">Ações</div>
        </div>
      </div>
    </div>

    <p v-if="listaPedidosRealizados.length === 0" id="lista-vazia">
      Nenhum pedido por aqui ainda. Que tal montar o primeiro açaí? 🍇
    </p>

    <div
      class="pedidos-tabela-linha"
      v-for="pedido in listaPedidosRealizados"
      :key="pedido.id"
    >
      <div id="ordem-numero">{{ pedido.id }}</div>
      <div>{{ pedido.nome }}</div>
      <div>{{ pedido.acai.nome }}</div>
      <div>{{ pedido.tamanho.descricao }}</div>
      <div>
        <ul>
          <li v-for="(complemento, index) in pedido.complemento" :key="index">
            {{ complemento.nome }}
          </li>
        </ul>
        <div class="divider"></div>
        <ul>
          <li v-for="(bebida, index) in pedido.bebidas" :key="index">
            {{ bebida.nome }}
          </li>
        </ul>
      </div>
      <div>
        <select
          name="status"
          class="status"
          @change="atualizarStatusPedido($event, pedido.id)"
        >
          <option value="">Selecione</option>
          <option
            v-for="status in listaStatusPedido"
            :key="status.id"
            :value="status.id"
            :selected="status.id == pedido.statusId"
          >
            {{ status.descricao }}
          </option>
        </select>
      </div>
      <div id="div-acoes">
        <img
          @click="deletarPedido(pedido.id)"
          src="/img/lixeira.svg"
          width="32px"
          height="32px"
          alt="Excluir pedido"
          title="Excluir pedido"
        />
      </div>
    </div>
  </div>
</template>
<script>
import AlertaComponent from "@/components/AlertaComponent.vue";

export default {
  name: "ListaPedidoComponent",
  components: {
    AlertaComponent,
  },
  data() {
    return {
      listaPedidosRealizados: [],
      listaStatusPedido: [],
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
    async consultarPedidos() {
      const response = await fetch(`${this.$apiUrl}/pedidos`);
      const dados = await response.json();
      this.listaPedidosRealizados = dados;
    },
    async consultarStatusPedido() {
      const response = await fetch(`${this.$apiUrl}/status_pedido`);
      this.listaStatusPedido = await response.json();
    },
    async deletarPedido(id) {
      try {
        const response = await fetch(`${this.$apiUrl}/pedidos/${id}`, {
          method: "DELETE",
        });

        if (!response.ok) {
          throw new Error("Falha ao excluir o pedido");
        }

        
        this.listaPedidosRealizados = this.listaPedidosRealizados.filter(
          (pedido) => pedido.id !== id
        );
        this.mostrarAlerta("sucesso", "Pedido excluído com sucesso!");
      } catch (erro) {
        this.mostrarAlerta(
          "erro",
          "Não foi possível excluir o pedido. Tente novamente."
        );
      }
    },
    async atualizarStatusPedido(event, idPedido) {
      const idStatusAtualizado = event.target.value;
      const atualizacaoJson = JSON.stringify({ statusId: idStatusAtualizado });
      try {
        const response = await fetch(`${this.$apiUrl}/pedidos/${idPedido}`, {
          method: "PATCH",
          headers: { "Content-Type": "application/json" },
          body: atualizacaoJson,
        });

        if (!response.ok) {
          throw new Error("Falha ao atualizar o status");
        }

      
        const pedido = this.listaPedidosRealizados.find((p) => p.id === idPedido);
        if (pedido) pedido.statusId = idStatusAtualizado;
        this.mostrarAlerta("info", "Status do pedido atualizado.");
      } catch (erro) {
        this.mostrarAlerta("erro", "Não foi possível atualizar o status.");
      }
    },
  },
  mounted() {
    this.consultarPedidos();
    this.consultarStatusPedido();
  },
};
</script>
<style scoped>
#pedidos-tabela {
  width: 100%;
  margin: 0 auto;
}

#pedidos-tabela-cabecalho,
.pedidos-tabela-linha {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
}

#pedidos-tabela-cabecalho {
  font-weight: bold;
  padding: 12px;
  border-bottom: 2px solid #5b1d8a;
  color: #3a1457;
}

#pedidos-tabela-cabecalho div,
.pedidos-tabela-linha div {
  width: 18%;
  text-align: left;
}

.pedidos-tabela-linha {
  width: 100%;
  padding: 12px;
  border-bottom: 1px dotted #c9b3dd;
}
.pedidos-tabela-linha:hover {
  background: #f6eefc;
}

#pedidos-tabela-cabecalho #ordem-id,
.pedidos-tabela-linha #ordem-numero,
.pedidos-tabela-linha #div-acoes,
#pedidos-tabela-cabecalho #div-acoes {
  width: 5%;
}

.pedidos-tabela-linha ul {
  margin: 0;
  padding-left: 16px;
  font-size: 13px;
}

.status {
  padding: 8px;
  border-radius: 8px;
  border: solid #c9b3dd 1px;
}

#div-acoes img {
  cursor: pointer;
  transition: 0.2s;
}
#div-acoes img:hover {
  transform: scale(1.15);
}

#lista-vazia {
  text-align: center;
  color: #6a4c93;
  font-size: 17px;
  padding: 40px 0;
}
</style>
