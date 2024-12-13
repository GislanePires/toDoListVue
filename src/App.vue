<script setup>
import { watch, onMounted, ref, computed } from "vue";
// import "./styles/GlobalStyle.scss";
// import "../src/styles/GlobalStyle.scss";

import doguito from "../src/assets/doguito-sentado2.png";

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

// Computed() pra filtro de to dos com base no input
const tarefasFiltradas = computed(() => {
  const textoFiltro = novaTarefa.value.toLowerCase().trim();
  if (textoFiltro === "") {
    return tarefas.value; //quando o campo estiver vazio, retorna todos os to dos
  }
  return tarefas.value.filter((tarefa) =>
    tarefa.texto.toLowerCase().includes(textoFiltro)
  );
});

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
    <section class="container-todo">
      <h1>🐾 ToDo <span>guito</span></h1>
      <!-- Adicionar ou filtrar -->
      <section class="container-inputs">
        <input
          type="text"
          placeholder="Adicione uma nova tarefa"
          v-model="novaTarefa"
          class="input-add-text"
        />
        <input type="date" v-model="novaData" class="input-add-data" />
        <button @click="adicionarTarefa" class="button-add">Adicionar</button>
        <!-- v-model - diretiva que sincroniza um valor de uma variável ao input -->
      </section>
      <!-- tudo o que for reativo,dentro de script, precisa do .value p/acessar o valor, mas no template, não é necessário -->
      <div v-if="tarefas.length === 0">
        <p>Parabéns! Você completou todas as tarefas!!!!</p>
      </div>

      <ul v-else-if="tarefasFiltradas.length > 0" class="scrollable-list">
        <!-- v-for - é uma diretiva que serve para fazer loops-->
        <li v-for="(tarefa, index) in tarefasFiltradas" :key="index" class="item-tarefa">
          <div :class="{ concluida: tarefa.concluida }">
            <input
              type="checkbox"
              @change="toggleConcluida(index)"
              :checked="tarefa.concluida"
              class="check-style"
            />

            {{ tarefa.texto }}
          </div>
          <span class="data" v-if="tarefa.data"> ({{ tarefa.data }})</span>
          <button @click="RemoverTarefa(index)" class="delete-button">
            X
          </button>
        </li>
      </ul>
      <div v-else>
        <p>Nenhuma tarefa encontrada</p>
      </div>
    </section>
    <div class="elipse-doguito">
      <img :src="doguito" alt="" />
    </div>
    <!-- <div class="elipse-top"></div> -->
  </main>
</template>

<style scoped lang="scss">
main {
  height: 100vh;
  width: 80%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  @media (max-width: 768px){
    width: 100%;
    justify-content: flex-start;
    margin-top: 0;
    
  }

  .container-inputs {
    width: 60%;
    height: auto;
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 0.5rem;

    @media (max-width: 1280px){
      flex-direction: column;
      /* background-color: aqua; */
      height: 90%;
      margin: 1rem;
    }

    .input-add-data,
    .input-add-text {
      height: 1.8rem;
      border-radius: 0.6rem;
      border: 0.2rem solid rgb(207, 191, 180);
    }
    .button-add {
      border-radius: 0.6rem;
      border: none;
      padding: 0.5rem;
      background-color: rgb(207, 191, 180);
      transition: all 0.3s ease;
      box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2);
      cursor: pointer;

      &:hover {
        background-color: rgb(142, 131, 124);
        box-shadow: 3px 3px 7px rgba(0, 0, 0, 0.3);
      }

      &:active {
        outline: none;
        color: rgb(246, 245, 244);
        background-color: rgb(67, 62, 58);
        box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.4);
        transform: translateY(2px);
      }
    }
  }
}

.item-tarefa {
  background-color: rgb(176, 162, 153);
  max-height: 3rem;
  width: 80%;
  height: 30%;
  border-radius: 0.7rem;
  padding: 0.5rem 0.9rem 0.5rem 0.9rem;
  display: flex;
  justify-content: space-between;
  align-items: center;

  @media (max-width: 768px){
    font-size: 0.8rem;
  }

  .data {
    color: azure;
  }
}
.elipse-doguito {
  background-color: rgb(207, 191, 180);
  width: 40rem;
  height: 40rem;
  border-radius: 50%;
  position: absolute;
  transform: translateY(50%);
  right: 0;
  left: 70%;

  @media (max-width: 768px){
    width: 20rem;
    height: 20rem;
    right: 0;
    top: 35rem;
    transform: translateX(-90%);
  }

  img {
    width: 35rem;
    height: 35rem;
    position: relative;
    bottom: 40%;
    right: 4rem;

    @media (max-width: 768px){
      width: 10rem;
      height: 10rem;
      left: 3rem;
      bottom: 3rem;
    }
  }
}
.elipse-top {
  background-color: rgb(207, 191, 180);
  width: 40rem;
  height: 40rem;
  border-radius: 50%;
  position: absolute;
  right: 73rem;
  transform: translateY(-50%);

  @media (max-width: 768px){
    width: 15rem;
    height: 15rem;
    right: 0;
    top: 35rem;
    transform: translateX(-90%);
  }
}
.container-todo {
  height: 80%;
  width: 60%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
  border-radius: 3rem;
  /* background-color: rgb(207, 191, 180); */
  position: relative;
  padding: 1rem;

  @media (max-width: 768px){
    border: none;
    width: 90%;
    padding: 0;
    border-radius:0;
  }
    h1 {
      font-size: 3rem;

      @media (max-width: 768px){
        font-size: 1.6rem;
        width: 100vw;
        height: 80%;
        text-align: center;
        background-color: rgb(199, 186, 177);
        color: rgb(35, 32, 31);
        display: flex;
        justify-content: center;
        align-items: center;
        margin: 0;
        border-radius: 50%;
        border-top-right-radius: 0;
        border-top-left-radius: 0;
      }

      span {
        color: rgb(142, 130, 122);
        @media (max-width: 768px){
          color: beige;
        }
      }
    }

  .delete-button {
    border: none;
    border-radius: 0.3rem;
    background-color: transparent;
    cursor: pointer;
    color: azure;
    font-weight: 700;
    padding: 0.2rem 0.5rem;
    margin-left: 1rem; 
    transition: color 0.8s ease;

    &:hover {
      background-color: rgb(51, 51, 51);
    }

    &:focus {
      outline: none;
    }
  }

  .scrollable-list {
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    gap: 1rem;
    max-height: 300px;
    overflow-y: auto;
    height: 100%;
    width: 100%;
    padding-left: 4rem;

    &::-webkit-scrollbar {
      height: 10px;
      width: 10px;
    }
    &::-webkit-scrollbar-track {
      border-radius: 5px;
      background-color: #dfe9eb;
    }
    &::-webkit-scrollbar-track:hover {
      background-color: #b8c0c2;
    }
    &::-webkit-scrollbar-track:active {
      background-color: #767a7c;
    }
    &::-webkit-scrollbar-thumb {
      border-radius: 5px;
      background-color: rgb(207, 191, 180);
    }
    &::-webkit-scrollbar-thumb:hover {
      background-color: rgb(121, 111, 103);
    }
    &::-webkit-scrollbar-thumb:active {
      background-color: rgb(145, 132, 123);
    }
  }
  .check-style {
    width: 1rem;
    height: 1rem;
    accent-color: rgb(145, 132, 123);
    padding: 0.5rem;
  }

  .check-style:checked {
    accent-color: rgb(207, 191, 180);
  }
  li {
    list-style: none;
    /* background-color: aqua; */
  }
}

.concluida {
  text-decoration: line-through;
  color: rgb(67, 67, 67);
}

.data {
  color: #888;
  font-size: 0.9rem;
}
</style>
