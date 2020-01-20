<template>
  <q-page>
    <q-card-section>
      <div style="width: 50%">
        <br />
        <label>GESTÃO DE CONTATOS</label>
        <q-separator />
        <br />
        <br />
        <q-input
          v-model="contato.nome"
          label="Nome"
          :rules="[val => !!val || 'Campo Obrigatório']"
        />
        <q-input
          v-model="contato.valor"
          label="Valor"
          :rules="[val => !!val || 'Campo Obrigatório']"
        />
        <q-input
          v-model="contato.obs"
          label="Observacao"
          :rules="[val => !!val || 'Campo Obrigatório']"
        />
        <q-select v-model="contato.canal" :options="canais" label="Canal" />
      </div>
      <br />
      <q-btn color="primary" label="Salvar" @click="salvar" />
      <br />
      <br />
      <div v-if="contatos.length !== 0">
        <template>
          <q-table
            title="Contatos"
            :data="contatos"
            :columns="columns"
            row-key="name"
          />
        </template>
      </div>
    </q-card-section>
  </q-page>
</template>

<script>
import axios from "axios";
export default {
  name: "PageIndex",
  created: function() {
    this.loadData();
  },
  data() {
    return {
      contatos: [],
      contato: {
        nome: null,
        valor: null,
        obs: null,
        canal: null
      },
      canais: ["CELULAR", "EMAIL", "FIXO"],
      columns: [
        {
          name: "nome",
          required: true,
          label: "Nome",
          align: "left",
          field: "nome",
          sortable: true,
          classes: "my-class",
          style: "width: 500px"
        },
        {
          name: "canal",
          required: true,
          label: "Canal",
          align: "left",
          field: "canal",
          sortable: true,
          classes: "my-class",
          style: "width: 500px"
        },
        {
          name: "valor",
          required: true,
          label: "Valor",
          align: "left",
          field: "valor",
          sortable: true,
          classes: "my-class",
          style: "width: 500px"
        },
        {
          name: "obs",
          required: true,
          label: "Observação",
          align: "left",
          field: "obs",
          sortable: true,
          classes: "my-class",
          style: "width: 500px"
        }
      ],
      leftDrawerOpen: false
    };
  },
  methods: {
    limpar() {
      this.contato = {
        nome: "",
        valor: "",
        obs: "",
        canal: ""
      };
    },
    async salvar() {
      await axios
        .post("http://localhost:8888/gestao_contato/cadastrar", {
          nome: this.contato.nome,
          valor: this.contato.valor,
          obs: this.contato.obs,
          canal: this.contato.canal
        })
        .then(function(response) {
          console.log(response + " - " + this.contato);
        })
        .catch(function(error) {
          console.log(error);
        });
      this.limpar();
      this.loadData();
    },
    async loadData() {
      await axios
        .get("http://localhost:8888/gestao_contato/listar")
        .then(response => {
          this.contatos = response.data;
        })
        .catch(() => {});
    }
  }
};
</script>
