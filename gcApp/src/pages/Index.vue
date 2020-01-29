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
      <q-btn color="primary" label="Cadastrar" @click="salvar" />
      <br />
      <br />
      <template>
        <div v-if="contatos.length !== 0">
          <el-table :data="contatos" style="width: 100%">
            <el-table-column fixed prop="nome" label="Nome" width="200">
            </el-table-column>
            <el-table-column prop="valor" label="Valor" width="200">
            </el-table-column>
            <el-table-column prop="obs" label="Observação" width="200">
            </el-table-column>
            <el-table-column prop="canal" label="Canal" width="200">
            </el-table-column>
            <el-table-column fixed="right" label="Ações" width="200">
              <template slot-scope="scope">
                <el-button
                  @click="atualizar(scope.$index, contatos)"
                  type="text"
                  size="small"
                  >Editar</el-button
                >
                <el-button
                  type="text"
                  size="small"
                  @click.native.prevent="deleteRow(scope.$index, contatos)"
                  >Excluir</el-button
                >
              </template>
            </el-table-column>
          </el-table>
        </div>
      </template>

      <template>
        <q-dialog
          v-model="abreModal"
          persistent
          v-show="this.contatoSelecionado != null"
        >
          <q-card>
            <q-card-section class="row items-center">
              <q-avatar icon="delete" color="white" text-color="red" />
              <span class="q-ml-sm"
                >Deseja excluir o contato: {{ contatoSelecionado.nome }}?</span
              >
            </q-card-section>

            <q-card-actions align="right">
              <q-btn
                flat
                label="Excluir"
                @click="excluir"
                color="red"
                v-close-popup
              />
              <q-btn flat label="Fechar" color="black" v-close-popup />
            </q-card-actions>
          </q-card>
        </q-dialog>
      </template>

      <template>
        <q-dialog
          v-model="editarModal"
          persistent
          v-show="this.editarModal != false"
        >
          <q-card style="width: 50%">
            <q-card-section class="row items-center">
              <q-avatar icon="restore" color="white" text-color="green" />
              <span class="q-ml-sm">Editar contato: </span>
            </q-card-section>

            <br />
            <q-card-section>
              <q-input v-model="contatoSelecionado.nome" label="Nome" />
              <q-input v-model="contatoSelecionado.valor" label="Valor" />
              <q-input v-model="contatoSelecionado.obs" label="Observacao" />
              <q-select
                v-model="contatoSelecionado.canal"
                :options="canais"
                label="Canal"
              />
            </q-card-section>

            <br />

            <q-card-actions align="right">
              <q-btn
                flat
                label="Atualizar"
                @click="salvar"
                color="primary"
                v-close-popup
              />
              <q-btn flat label="Fechar" color="black" v-close-popup />
            </q-card-actions>
          </q-card>
        </q-dialog>
      </template>
    </q-card-section>
  </q-page>
</template>

<script>
import axios from "axios";
export default {
  name: "PageIndex",
  created() {
    this.loadData();
  },
  data() {
    return {
      contatoSelecionado: {
        id: null,
        nome: null,
        valor: null,
        obs: null,
        canal: null
      },
      abreModal: false,
      editarModal: false,
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
    deleteRow(index, rows) {
      this.abreModal = true;
      for (let tot = 0; tot < rows.length; tot++) {
        if (index == tot) {
          this.contatoSelecionado = rows[tot];
        }
      }
    },
    limpar() {
      this.contato = {
        id: null,
        nome: null,
        valor: null,
        obs: null,
        canal: null
      };
      this.contatoSelecionado = {
        id: null,
        nome: null,
        valor: null,
        obs: null,
        canal: null
      };
    },
    atualizar(index, rows) {
      for (let tot = 0; tot < rows.length; tot++) {
        if (index == tot) {
          this.contatoSelecionado = rows[tot];
        }
      }
      if (this.contatoSelecionado != null) {
        this.editarModal = true;
      }
    },
    async excluir() {
      await axios
        .delete(
          "http://localhost:8888/gestao_contato/excluir/" +
            this.contatoSelecionado.id
        )
        .then(response => {
          this.loadData();
          console.log(response);
        })
        .catch(() => {});
    },
    async salvar() {
      debugger;
      if (this.contatoSelecionado.id != null) {
        this.contato = this.contatoSelecionado;
      }
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
