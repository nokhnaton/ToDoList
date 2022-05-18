<script setup>
import { ref } from "vue";

const tasks = ref([
  { name: "寝る", deadline: "20:00" },
  { name: "起きる", deadline: "16:00" },
]);

const completedTasks = ref([]);
const newTodoName = ref("");
const newTodoDeadline = ref("");

const addTask = () => {
  tasks.value.push({
    name: newTodoName.value,
    deadline: newTodoDeadline.value,
  });
};
const completeTask = (name, deadline, index) => {
  completedTasks.value.push({
    name: name,
    deadline: deadline,
  });
  tasks.value.splice(index, 1);
};
const uncompleteTask = (name, deadline, index) => {
  tasks.value.push({
    name: name,
    deadline: deadline,
  });
  completedTasks.value.splice(index, 1);
};
const deleteTask = (index) => {
  completedTasks.value.splice(index, 1);
};
</script>

<template>
  <div v-for="(task, index) in tasks" :key="task.name">
    <div class="task">
      <span class="name list">名前: {{ task.name }}</span>
      <span class="deadline list">期限: {{ task.deadline }}</span>
      <button
        class="complete"
        @click="completeTask(task.name, task.deadline, index)"
      >
        完了
      </button>
    </div>
  </div>
  <div>
    <label>
      名前
      <input v-model="newTodoName" type="text" />
    </label>
    <label>
      期限
      <input v-model="newTodoDeadline" type="text" />
    </label>
    <button @click="addTask">追加</button>
  </div>
  <div class="cTask">終わったタスク</div>
  <div v-for="(cTask, index) in completedTasks" :key="cTask.name" class="cTask">
    <div class="task">
      <span class="name list">名前: {{ cTask.name }}</span>
      <span class="deadline list">期限: {{ cTask.deadline }}</span>
      <button class="delete" @click="deleteTask(index)">削除</button>
      <button
        class="uncomplete"
        @click="uncompleteTask(cTask.name, cTask.deadline, index)"
      >
        終わってなかった!
      </button>
    </div>
  </div>
</template>

<style>
.over500 {
  color: red;
}
.list {
  padding: 1rem;
}
.cTask {
  color: gray;
}
.delete {
  color: yellow;
  background-color: red;
}
.uncomplete {
  color: red;
}
.complete {
  color: black;
  background-color: dodgerblue;
}
button {
  border-radius: 50vh;
}
</style>
