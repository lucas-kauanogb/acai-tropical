# 🍇 Açaí Tropical

Sistema de pedidos online para uma **açaiteria**, desenvolvido em **Vue 3**.
O projeto é uma evolução da base **T-Burguer** trabalhada em sala, migrada de uma
hamburgueria para uma loja de açaí — com nova identidade visual, novo modelo de
dados, validação de formulário, sistema de alertas semânticos e fluxo de UX com
redirecionamento e atualização em tempo real.

---

## 🔗 Links do Projeto

| Recurso | Link |
| ------- | ---- |
| 🌐 **Aplicação em produção (Vercel)** | `https://acai-tropical-drab.vercel.app?_vercel_share=iacAzCvJhyMvyq4QyysQ9JazdaZ3AuW9` |
| 🗄️ **API mockada (Render / JSON Server)** | `https://banco-json.onrender.com` |
| 💻 **Repositório do front-end** | `https://github.com/lucas-kauanogb/acai-tropical` |
| 🗃️ **Repositório do banco-json** | `https://github.com/lucas-kauanogb/banco-json` |

---

## 1. Visão Geral

O segmento escolhido foi uma **Açaiteria**. A lógica de negócio da hamburgueria foi
reinterpretada para o universo do açaí:

| Antes (T-Burguer) | Depois (Açaí Tropical) |
| ----------------- | ---------------------- |
| Hambúrgueres no cardápio | Tigelas de açaí (`menu.acais`) |
| **Ponto da carne** | **Tamanho da tigela** (300ml, 500ml, 700ml, 1L) |
| Complementos / Bebidas | Toppings (granola, Nutella, morango...) / Bebidas |
| Identidade escura + dourada | Identidade roxo açaí + verde tropical |

### Principais alterações estruturais

**Modelo de dados (`db.json`)** — a coleção de pontos da carne deu lugar a `tamanhos`,
e cada item do cardápio passou a ser um açaí:

```json
"tamanhos": [
  { "id": 1, "descricao": "Pequeno - 300ml", "valor_extra": 0 },
  { "id": 2, "descricao": "Médio - 500ml", "valor_extra": 5 }
],
"menu": {
  "acais": [
    { "id": 1, "nome": "Tradicional", "foto": "/img/acai-1.svg", "valor": 18, "eh_novidade": false }
  ]
}
```

**URL da API via variável de ambiente** — o endereço da API é exposto globalmente
como `this.$apiUrl` em `main.js`, lendo da variável `VUE_APP_API_BASE_URL`. Assim o
mesmo código funciona tanto local quanto em produção:

```js
// src/main.js
const app = createApp(App);
app.config.globalProperties.$apiUrl = process.env.VUE_APP_API_BASE_URL;
app.use(router).mount("#app");
```

```js
// uso nos componentes
const response = await fetch(`${this.$apiUrl}/menu`);
```

**Identidade visual** — novo logo e ilustrações em **SVG** (em `public/img`), banner
roxo, selo de "Novidade" nos cards e paleta semântica consistente.

---

## 2. Solução Técnica dos Alertas

O feedback visual é centralizado no componente reutilizável `AlertaComponent.vue`,
que recebe via *props* o **tipo** (que define a cor e o ícone) e a **mensagem**. Os
quatro tipos cobrem a paleta semântica exigida:

| Tipo | Cor | Uso |
| ---- | --- | --- |
| `erro` | 🔴 Vermelho | Campos obrigatórios ausentes / ação inválida |
| `aviso` | 🟠 Laranja | Avisos importantes (ex.: açaí sem complementos) |
| `info` | 🔵 Azul | Informações contextuais (boas-vindas, status) |
| `sucesso` | 🟢 Verde | Pedido criado, atualizado ou excluído |

O ícone é escolhido dinamicamente conforme o tipo:

```vue
<template>
  <div v-if="visivel" :class="['alerta', `alerta-${tipo}`]" role="alert">
    <span class="alerta-icone">{{ obterIcone() }}</span>
    <span class="alerta-mensagem">{{ mensagem }}</span>
  </div>
</template>

<script>
export default {
  props: { tipo: String, mensagem: String, visivel: Boolean },
  methods: {
    obterIcone() {
      const icones = { erro: "✕", aviso: "⚠", info: "ℹ", sucesso: "✓" };
      return icones[this.tipo] || "ℹ";
    },
  },
};
</script>
```

Cada componente que precisa dar feedback mantém um objeto `alerta` no `data()` e um
método `mostrarAlerta(tipo, mensagem)` que o atualiza de forma reativa:

```js
data() {
  return { alerta: { visivel: false, tipo: "info", mensagem: "" } };
},
methods: {
  mostrarAlerta(tipo, mensagem) {
    this.alerta = { visivel: true, tipo, mensagem };
  },
}
```

### Validação dos campos obrigatórios

Antes de enviar o pedido, `validarPedido()` checa os campos essenciais e retorna
`false` (bloqueando o envio) exibindo o alerta correspondente:

```js
validarPedido() {
  if (!this.acai || !this.acai.nome) {
    this.mostrarAlerta("aviso", "Volte ao Cardápio e selecione um açaí antes de continuar.");
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
}
```

Se o pedido for válido mas estiver **sem nenhum complemento ou bebida**, um alerta
**laranja** pede confirmação antes de prosseguir. Em caso de sucesso no `POST`, um
alerta **verde** é exibido e o usuário é redirecionado automaticamente.

---

## 3. Diretrizes de Experiência do Usuário (UX)

**Redirecionamento inteligente** — ao confirmar com sucesso, o alerta verde aparece e
a navegação para a listagem acontece automaticamente:

```js
this.mostrarAlerta("sucesso", "Pedido confirmado com sucesso! Redirecionando...");
setTimeout(() => { this.$router.push("/pedidos"); }, 1200);
```

**Atualização em tempo real** — a tela de pedidos recarrega a lista no `mounted()`,
então o novo pedido já aparece assim que a página é aberta.

**Feedback de remoção** — ao excluir, a interface é **re-renderizada na hora**
filtrando o item do array local:

```js
this.listaPedidosRealizados = this.listaPedidosRealizados.filter(
  (pedido) => pedido.id !== id
);
this.mostrarAlerta("sucesso", "Pedido excluído com sucesso!");
```

---

## 4. Como rodar localmente

Pré-requisitos: **Node.js** instalado.

```bash
# 1. Instalar dependências
npm install

# 2. Subir a API mockada (JSON Server) — deixe rodando em um terminal
npm run bancojson      # http://localhost:3000

# 3. Em outro terminal, subir o front-end
npm run serve          # http://localhost:8080
```

> O arquivo `.env.development` já aponta `VUE_APP_API_BASE_URL` para
> `http://localhost:3000`. Use `.env.exemplo` como referência.

---

## 🛠️ Tecnologias

- **Vue 3** (Options API) + **Vue Router 4**
- **JSON Server** (API mockada)
- **Fetch API** para o consumo dos dados
- **CSS scoped** por componente
- Ilustrações em **SVG**
