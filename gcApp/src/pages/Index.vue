<template>
  <q-page>
    <q-card-section>
      <div style="width: 50%">
        <br />
        <label>GESTÃO DE CONTATOS</label>
        <q-separator />
        <br />
        <br />
        <q-input v-model="contato.nome" label="Nome" />
        <q-input v-model="contato.valor" label="Valor" />
        <q-input v-model="contato.obs" label="Observacao" />
        <q-select v-model="contato.canal" :options="canais" label="Canal" />
      </div>
      <br />
      <q-btn color="primary" label="Salvar" @click="salvar" />
      <q-btn
        color="secondary"
        label="Atualizar"
        v-show="selected.length == 1"
        @click="atualizar"
      />
      <q-btn
        color="deep-orange"
        glossy
        label="Excluir"
        v-show="selected.length == 1"
        @click="excluir"
      />
      <br />
      <br />

      <template>
        <div v-if="contatos.length !== 0">
          <q-table
            title="Contatos"
            :data="contatos"
            :columns="columns"
            selection="single"
            :selected.sync="selected"
            row-key="nome"
          />
        </div>
      </template>
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
  deleteRow() {
    alert("Veio");
  },
  data() {
    return {
      selected: [],
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
        id: null,
        nome: null,
        valor: null,
        obs: null,
        canal: null
      };
      this.selected = [];
    },
    atualizar() {
      this.contato = this.selected[0];
      console.log(this.contato);
    },
    async excluir() {
      await axios
        .delete(
          "http://localhost:8888/gestao_contato/excluir/" + this.selected[0].id
        )
        .then(response => {
          console.log(response);
        })
        .catch(() => {});
      this.loadData();
    },
    async salvar() {
      if (
        this.contato.nome != null &&
        this.contato.valor != null &&
        this.contato.canal != null
      ) {
        await axios
          .post("http://localhost:8888/gestao_contato/cadastrar", {
            id: this.contato.id,
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
        this.loadData();
      } else {
        alert("Preencha todos os campos");
      }
    },
    async loadData() {
      this.limpar();
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
