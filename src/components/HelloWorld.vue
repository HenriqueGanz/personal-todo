<script setup>
import { ref } from "vue";

const saveToLocalStorage = (key, value) => {
  localStorage.setItem(key, JSON.stringify(value));
};

const loadFromLocalStorage = (key) => {
  const data = localStorage.getItem(key);
  return data ? JSON.parse(data) : [];
};

const createTodo = () => {
  const newTodo = {
    text: "",
    completed: false,
    createdAt: new Date().toISOString().split("T")[0],
    completedAt: null,
  };
  list.value.push(newTodo);
  saveToLocalStorage("todoList", list.value);
};

const toggleTodo = (todo) => {
  todo.completed = !todo.completed;
  todo.completedAt = todo.completed
    ? new Date().toISOString().split("T")[0]
    : null;
  saveToLocalStorage("todoList", list.value);
};

const removeTodo = (todo) => {
  list.value = list.value.filter((item) => item !== todo);
  saveToLocalStorage("todoList", list.value);
  count.value = list.value.filter((item) => !item.completed).length;
};

const saveTodoText = () => {
  saveToLocalStorage("todoList", list.value);
};

const count = ref(0);
const list = ref(loadFromLocalStorage("todoList"));
console.log(list.value);
</script>

<template>
  <div class="container">
    <div class="card">
      <button type="button" @click="createTodo()">Create task</button>
    </div>

    <div v-if="list.length > 0">
      <div class="card" v-for="(todo, index) in list" :key="index">
        <div class="times">
          <span class="created-at">
            <p>Created</p>
            <p>
              {{ todo.createdAt }}
            </p>
          </span>
          <span v-if="todo.completedAt" class="completed-at">
            <p>Completed</p>
            <p>
              {{ todo.completedAt }}
            </p>
          </span>
        </div>
        <div class="text-check">
          <input
            type="text"
            :class="{ completed: todo.completed }"
            class="todo-text"
            v-model="todo.text"
            @keyup.enter="saveTodoText()"
          />
          <input
            type="checkbox"
            :checked="todo.completed"
            @change="toggleTodo(todo)"
          />
        </div>
        <button type="button" @click="removeTodo(todo)">Remove</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  margin: 10px;
  padding: 10px;
  border-radius: 5px;
  display: flex;
  flex-direction: column;
}
.completed {
  text-decoration: line-through;
}

.todo-text {
  width: 100%;
  padding: 5px;
  border-radius: 5px;
  border: none;
  outline: none;
  font-size: medium;
}

.text-check {
  display: flex;
  align-items: center;
}

.created-at {
  font-size: small;
  word-break: keep-all;
}

.completed-at {
  font-size: small;
  word-break: keep-all;
}

.times {
  display: flex;
  justify-content: space-around;
  margin: 2px;
  background-color: rgba(0, 0, 0, 0.1);
  padding: 2px;
  border-radius: 10px;
  width: 100%;
}

.times span p {
  margin: 0;
}

.container {
  width: 80%;
}
</style>
