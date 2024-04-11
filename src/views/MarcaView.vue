<script setup>
import { ref, reactive, onMounted } from "vue";
import MarcasApi from "@/api/marcas";
const marcasApi = new MarcasApi();

const defaultMarca = { id: null, nome: "", nacionalidade: "" || null };
const marcas = ref([]);
const marca = reactive({ ...defaultMarca });

onMounted(async () => {
  marcas.value = await marcasApi.buscarTodasAsMarcas();
});

function limpar() {
  Object.assign(marca, { ...defaultMarca });
}

async function salvar() {
  if (marca.id) {
    await marcasApi.atualizarMarca(marca);
  } else {
    await marcasApi.adicionarMarca(marca);
  }
  marcas.value = await marcasApi.buscarTodasAsMarcas();
  limpar();
}

function editar(marca_para_editar) {
  Object.assign(marca, marca_para_editar);
}

async function excluir(id) {
  await marcasApi.excluirMarca(id);
  marcas.value = await marcasApi.buscarTodasAsMarcas();
  limpar();
}
</script>

<template>
  <h1>Marca</h1>
  <hr />
  <div class="form">
    <input type="text" v-model="marca.nome" placeholder="Nome" />
    <input type="text" v-model="marca.nacionalidade" placeholder="Nacionalidade" />
    <button @click="salvar">Salvar</button>
    <button @click="limpar">Limpar</button>
  </div>
  <hr />
  <ul>
    <li v-for="marca in marcas" :key="marca.id">
      <span class="nome" @click="editar(marca)">
        ({{ marca.id }}) Nome: {{ marca.nome }} / Nacionalidade: {{ marca.nacionalidade }}
      </span>
      <button class="botão" @click="excluir(marca.id)">X</button>
    </li>
  </ul>
</template>

<style>

.botão{
margin-left: 10px;
}

</style>
