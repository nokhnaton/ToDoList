<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
//import axios from "https://unpkg.com/vue@next";
//import dist from "https://unpkg.com/axios/dist/axios.min.js";

// const cTasks = ref([
//   { name: "寝る", deadline: "20:00" },
//   { name: "起きる", deadline: "16:00" },
// ]);
const newTodoName = ref("");
const newTodoDeadline = ref("");
const completedTasks = ref([]);
const uncompletedTasks = ref([]);
//const tasks = ref([]);
const tasks = ref([]);
const refreshTasks = () => {
  axios
    .get("https://temma.trap.show/naro-todo-server/noc7t/tasks")
    .then((response) => {
      tasks.value = response.data;
      if (!tasks.value && !tasks.value[0]) return;
      completedTasks.value = [];
      uncompletedTasks.value = [];
      for (let i = 0; i < tasks.value.length; i++) {
        if (tasks.value[i].isComplete) {
          completedTasks.value.push(tasks.value[i]);
        } else {
          uncompletedTasks.value.push(tasks.value[i]);
        }
      }
    })
    // eslint-disable-next-line no-console
    .catch((error) => console.log(error));
};

const addTask = () => {
  // tasks.value.push({
  //   name: newTodoName.value,
  //   deadline: newTodoDeadline.value,
  // });
  axios
    .post("https://temma.trap.show/naro-todo-server/noc7t/tasks", {
      name: newTodoName.value,
      deadline: newTodoDeadline.value,
      id: String(new Date().getTime),
      isComplete: false,
    })
    .then(() => {
      refreshTasks();
    })
    .catch((err) => {
      console.log(err);
    });
  refreshTasks();
};
const completeTask = (name, deadline, id) => {
  // completedTasks.value.push({
  //   name: name,
  //   deadline: deadline,
  // });
  // tasks.value.splice(index, 1);
  axios
    .put("https://temma.trap.show/naro-todo-server/noc7t/tasks/" + id, {
      name: name,
      deadline: deadline,
      id: id,
      isComplete: true,
    })
    .then(() => {
      refreshTasks();
    })
    .catch((err) => {
      console.log(err);
    });
  refreshTasks();
};
const uncompleteTask = (name, deadline, id) => {
  // tasks.value.push({
  //   name: name,
  //   deadline: deadline,
  // });
  // completedTasks.value.splice(index, 1);
  axios
    .put("https://temma.trap.show/naro-todo-server/noc7t/tasks/" + id, {
      name: name,
      deadline: deadline,
      id: id,
      isComplete: false,
    })
    .then(() => {
      refreshTasks();
    })
    .catch((err) => {
      console.log(err);
    });
  refreshTasks();
};
const deleteTask = (id) => {
  axios
    .delete("https://temma.trap.show/naro-todo-server/noc7t/tasks/" + id)
    .then(() => {
      refreshTasks();
    })
    .catch((err) => {
      console.log(err);
    });
};
const info = ref("");
const deleteAllTask = () => {
  axios
    .delete("https://temma.trap.show/naro-todo-server/noc7t/tasks")
    .then(() => {
      refreshTasks();
    })
    .catch((err) => {
      console.log(err);
    });
};
onMounted(refreshTasks);
</script>

<template>
  <div v-for="task in uncompletedTasks" :key="task.name">
    <div class="task">
      <span class="name list">名前: {{ task.name }}</span>
      <span class="deadline list">期限: {{ task.deadline }}</span>
      <button
        class="complete"
        @click="completeTask(task.name, task.deadline, task.id)"
      >
        完了
      </button>
    </div>
  </div>
  <div>
    {{ info }}
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
  <div v-for="cTask in completedTasks" :key="cTask.name" class="cTask">
    <div class="task">
      <span class="name list">名前: {{ cTask.name }}</span>
      <span class="deadline list">期限: {{ cTask.deadline }}</span>
      <button class="delete" @click="deleteTask(cTask.id)">削除</button>
      <button
        class="uncomplete"
        @click="uncompleteTask(cTask.name, cTask.deadline, cTask.id)"
      >
        終わってなかった!
      </button>
    </div>
  </div>
  <div>
    <button class="complete" @click="deleteAllTask()">全消去</button>
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
