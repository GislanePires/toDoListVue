<script setup>
import { watch, onMounted, ref } from "vue";

const tarefas = ref([
  { texto: "Estudar Vue.js", concluida: false, data: "2024-12-10" },
  { texto: "Treinar", concluida: false, data: "2024-12-12" },
  { texto: "Cozinhar", concluida: false, data: "2024-12-11" },
  { texto: "Tomar Pré-treino", concluida: false, data: "2024-12-08" },
  { texto: "Tomar banho", concluida: false, data: "2024-12-09" },
]);

function RemoverTarefa(index) {
  tarefas.value.splice(index, 1);
}
const novaTarefa = ref("");
const novaData = ref("");

function adicionarTarefa() {
  if (novaTarefa.value.trim() !== "" && novaData.value !== "") {
    tarefas.value.push({ 
      texto: novaTarefa.value.trim(), 
      concluida: false,
    data: novaData.value,
    });
    novaTarefa.value = "";
    novaData.value = "";
  }
}

const toggleConcluida = (index) => {
  tarefas.value[index].concluida = !tarefas.value[index].concluida;
};
//Salvando no Local Storage
watch(
  tarefas,
  () => {
    const tarefasString = JSON.stringify(tarefas.value);
    localStorage.setItem("tarefas", tarefasString);
    console.log("Armazenamento no localStorage: ", tarefasString);
  },
  { deep: true }
);

onMounted(() => {
  const tarefasSalvas = JSON.parse(localStorage.getItem("tarefas"));
  console.log("Carregamento no localStorage: ", tarefasSalvas);
  if (tarefasSalvas) tarefas.value = tarefasSalvas;
});
</script>

<template>
  <main>
    <h1>📝 To Do List</h1>
    <!-- tudo o que for reativo,dentro de script, precisa do .value p/acessar o valor, mas no template, não é necessário -->
    <div v-if="tarefas.length === 0">
      <p>Parabéns! Você completou todas as tarefas!!!!</p>
    </div>

    <ul v-else>
      <!-- v-for - é uma diretiva que serve para fazer loops-->
      <li v-for="(tarefa, index) in tarefas" :key="index">
        {{ tarefa.texto }}
        <button @click="RemoverTarefa(index)">❌Remover</button>
        <input
          type="checkbox"
          @change="toggleConcluida(index)"
          :checked="tarefa.concluida"
        />
        <span :class="{ concluida: tarefa.concluida }">{{ tarefa.texto }}</span>
        <span class="data" v-if="tarefa.data"> ({{ tarefa.data }})</span>
      </li>
    </ul>
    <section>
      <input
        type="text"
        placeholder="Adicione uma nova tarefa"
        v-model="novaTarefa"
      />
      <input
        type="date"
        v-model="novaData"
      />
      <button @click="adicionarTarefa">Adicionar</button>
      <!-- v-model - diretiva que sincroniza um valor de uma variável ao input -->
    </section>
  </main>
</template>

<style scoped lang="scss">
main {
  height: 100vh;
  width: 100%;
}

.concluida {
  text-decoration: line-through;
  color: gray;
}

.data {
  color: #888;
  font-size: 0.9rem;
  margin-left: 5px;
}
</style>

