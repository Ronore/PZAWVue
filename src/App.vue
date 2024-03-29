<template>
  <div class="container">
    <template v-if="todo.length !== 0">
      <Accordion v-for="(tablica, tablicaIndex) in todo" :key="tablicaIndex" ref="accordionsList"
        :header="'Skończono ' + finishedTasksCount(tablicaIndex) + ' z ' + allTaskCount(tablicaIndex)"
        :open="isOpen(tablicaIndex)" @closeOpen="hideAccordion(tablicaIndex)" :name="tablica.NazwaTablicy">

        <section class="task-list">
          <ul v-if="finished(tablicaIndex)">
            <template v-for="(task, taskIndex) in tablica.Tasks" :key="taskIndex">
              <TodoItem :task="task.TaskText" :completed="task.finished" :tablicaIndex="tablicaIndex"
                :taskIndex="taskIndex" @changed="SwitchTask" @addNewTask="addNewTask"></TodoItem>
              <br>
            </template>
          </ul>
          <p v-else class="all-completed">Wszystkie zadania wykonane</p>
          <todoInput @create="addTask(tablicaIndex, $event)"></todoInput>
        </section>
      </Accordion>

    </template>
    <p v-else class="no-tasks">Brak zadań do wykonania</p>

    <div v-if="showWarning" class="warning">
      <p>Pomyśl nad skończeniem poprzednich zadań</p>
      <button @click='hideWarning'>Ok</button>
    </div>
  </div>
  <button @click="collapseAll">Zwiń wszystek</button>
</template>

<script setup>
import { ref, computed } from 'vue'
import Accordion from './components/Accordion.vue'
import TodoItem from './components/TodoItem.vue'
import TodoInput from './components/TodoInput.vue'

const todo = ref([
  {
    NazwaTablicy: 'Tablica 1',
    Tasks: [
      { TaskText: 'Zadanie 1', finished: false },
      { TaskText: 'Zadanie 2', finished: true }
    ]
  },
  {
    NazwaTablicy: 'Tablica 2',
    Tasks: [
      { TaskText: 'Zadanie 1', finished: true },
      { TaskText: 'Zadanie 2', finished: false }
    ]
  }
]);

const isOpen = (tablicaIndex) => {
  return todo.value[tablicaIndex].open || false;
};

const hideAccordion = (tablicaIndex) => {
  todo.value[tablicaIndex].open = false;
};

const finished = (tablicaIndex) => {
  return !todo.value[tablicaIndex].Tasks.every(task => task.finished);
};

const finishedTasksCount = (tablicaIndex) => {
  let count = 0;
  todo.value[tablicaIndex].Tasks.forEach(task => {
    if (task.finished) count++;
  });
  return count;
};

const allTaskCount = (tablicaIndex) => {
  return todo.value[tablicaIndex].Tasks.length;
};

const showWarning = ref(false);

const hideWarning = () => {
  showWarning.value = false;
};

const addTask = (tablicaIndex, text) => {
  if (text !== "") {
    todo.value[tablicaIndex].Tasks.push({ TaskText: text, finished: false });
  }
};

const addNewTask = (tablicaIndex) => {
  todo.value[tablicaIndex].Tasks.push({ TaskText: "Nowe zadanie", finished: false });
};

const SwitchTask = (tablicaIndex, taskIndex) => {
  todo.value[tablicaIndex].Tasks[taskIndex].finished = !todo.value[tablicaIndex].Tasks[taskIndex].finished;
};

const accordionsList = ref();

const collapseAll = () =>{
  for(const accordion of accordionsList.value){
        accordion.collapse();

}};
</script>
