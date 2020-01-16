<template>
  <q-page class="flex flex-center"> AQUI:: {{ this.contatos }} </q-page>
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
      leftDrawerOpen: false
    };
  },
  methods: {
    loadData() {
      debugger;
      axios
        .get("localhost:8888/gestao_contato/listar")
        .then(response => {
          this.contatos = response.data;
        })
        .catch(() => {});
    }
  }
};
</script>
