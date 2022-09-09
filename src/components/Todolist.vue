<script setup>
import { ref, reactive, onMounted, computed, watch } from "vue";
import axios from "axios";

const inputTodo = ref("");
const displayType = ref("all");
const todoList = reactive({
  todos: [],
});

axios.defaults.headers.common["Authorization"] =
  "Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxMDQwIiwic2NwIjoidXNlciIsImF1ZCI6bnVsbCwiaWF0IjoxNjYyNzAxMTIzLCJleHAiOjE2NjM5OTcxMjMsImp0aSI6IjAwN2U5Yjg0LTdlOWQtNDczZC04MjRjLTI5YjM4MWRmNDBlMyJ9.q2HsgOO72zTcr5zqBAq07Lb5iGYAgzG6nzPiVqVGjvE";

async function fetchTodo() {
  try {
    const data = (await axios.get("https://todoo.5xcamp.us/todos")).data.todos;
    console.log(data);
    todoList.todos = data;
  } catch (error) {
    console.log(error);
  }
}

onMounted(() => {
  fetchTodo();
});

const displayList = computed(() => {
  console.log(displayType.value);
  switch (displayType.value) {
    case "pending":
      return todoList.todos.filter((itm) => !itm.completed_at);
    case "complete":
      return todoList.todos.filter((itm) => itm.completed_at);
    default:
      return todoList.todos;
  }
});

async function SubmitTodo() {
  try {
    if (!inputTodo.value) {
      alert("please input");
      return false;
    }

    const config = {
      headers: {
        Authorization:
          "Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxMDQwIiwic2NwIjoidXNlciIsImF1ZCI6bnVsbCwiaWF0IjoxNjYyNzAxMTIzLCJleHAiOjE2NjM5OTcxMjMsImp0aSI6IjAwN2U5Yjg0LTdlOWQtNDczZC04MjRjLTI5YjM4MWRmNDBlMyJ9.q2HsgOO72zTcr5zqBAq07Lb5iGYAgzG6nzPiVqVGjvE",
      },
    };

    const res = await axios.post(
      "https://todoo.5xcamp.us/todos",
      {
        todo: {
          content: inputTodo.value,
        },
      },
      config
    );
    const dd = { ...res.data, completed_at: null };
    const ddd = [...todoList.todos];
    ddd.push(dd);
    todoList.todos = ddd;
    inputTodo.value = "";
  } catch (error) {
    console.log(error);
  }
}

function changeType(str) {
  displayType.value = str;
}

async function toggleStatus(id) {
  try {
    console.log(id);
    const res = await axios.patch(`https://todoo.5xcamp.us/todos/${id}/toggle`);
    const found = todoList.todos.findIndex((element, idx) => element.id === id);
    todoList.todos[found] = res.data;
  } catch (error) {
    console.log(error);
  }
}

async function deleteTodo(id) {
  try {
    const res = await axios.delete(`https://todoo.5xcamp.us/todos/${id}`);
    const found = todoList.todos.findIndex((element, idx) => element.id === id);
    alert(res.data.message);
    todoList.todos.splice(found, 1);
  } catch (error) {
    console.log(error);
  }
}
</script>

<template>
  <h1>{{ displayType }}</h1>
  <button @click="changeType('all')">all</button>
  <button @click="changeType('pending')">pending</button>
  <button @click="changeType('complete')">complete</button>

  <input type="text" v-model="inputTodo" />
  <button @click="SubmitTodo">submit</button>

  <ul>
    <li
      v-for="items in displayList"
      :key="items.id"
      :class="{ 'text-through': items.completed_at }"
    >
      {{ items.content }}
      <button @click="toggleStatus(items.id)">Toggle</button>
      <button @click="deleteTodo(items.id)" class="btn-danger text-white">
        Delete
      </button>
    </li>
  </ul>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}

.text-through {
  text-decoration: line-through;
}

.text-white {
  color: white;
}

.btn-danger {
  background: red;
}

.btn {
  display: inline-block;
  padding: 1rem;
  border-radius: 1rem;
}

li {
  margin-bottom: 16px;
}
</style>
