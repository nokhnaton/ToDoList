<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const newTodoName = ref("");
const newTodoDeadline = ref("");
const completedTasks = ref([]);
const uncompletedTasks = ref([]);
/* 
axios.get/post/put/delete().then(() => {ここで処理をしないとうまくいかない})
console.log()の下につく黄色い波線は無視して大丈夫そう
*/
const refreshTasks = () => {
  const tasks = ref([]); //サーバーデータの一時保存用の配列
  axios
    .get("https://temma.trap.show/naro-todo-server/noc7t/tasks")
    .then((response) => {
      tasks.value = response.data;
      if (!tasks.value && !tasks.value[0]) return;
      completedTasks.value = [];
      uncompletedTasks.value = []; //一旦配列を空にする
      for (let i = 0; i < tasks.value.length; i++) {
        if (tasks.value[i].isComplete) {
          completedTasks.value.push(tasks.value[i]);
        } else {
          uncompletedTasks.value.push(tasks.value[i]);
        } //isCompleteの値で達成/未達成の配列に振り分ける
      }
    })
    .catch((error) => console.log(error));
};

const addTask = () => {
  const time = new Date().getTime();
  axios
    .post("https://temma.trap.show/naro-todo-server/noc7t/tasks", {
      name: newTodoName.value,
      deadline: newTodoDeadline.value,
      id: String(time), //登録した時の時間をidとして設定してる
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
  axios
    .put("https://temma.trap.show/naro-todo-server/noc7t/tasks/" + id, {
      name: name,
      deadline: deadline,
      id: id,
      isComplete: true,
    }) //isCompleteの値を更新,実はcompleteTaskとuncompleteTaskは統合できる
    .then(() => {
      refreshTasks();
    })
    .catch((err) => {
      console.log(err);
    });
  refreshTasks();
};
const uncompleteTask = (name, deadline, id) => {
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
      {{ task.id }}
      <button
        class="complete"
        @click="completeTask(task.name, task.deadline, task.id)"
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
