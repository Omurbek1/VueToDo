<script setup>
import { ref, onMounted, computed, watch } from "vue";

const name = ref("");
const todos = ref([]);

const input_content = ref("");
const input_category = ref(null);

const todo_asc = computed(() =>
  todos.value.sort((a, b) => b.createdAt - a.createdAt)
);
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createdAt: new Date().getTime(),
    done: false,
  });
  input_content.value = "";
  input_category.value = null;
};
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);
watch(name, () => {
  localStorage.setItem("name", name.value);
});
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2>Whats up, <input type="text" v-model="name" /></h2>
    </section>

    <section class="create-todo">
      <h1 class="title">Create Todo</h1>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo?</h4>
        <input type="text" v-model="input_content" />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
          {{ input_category }}
        </div>
        <input type="submit" value="Add Todo" />
      </form>
    </section>
    <section class="todo-list">
      <h1>Todo List</h1>
      <div class="list" id="todo-list">
        <div
          v-for="todo in todo_asc"
          :key="todo.createdAt"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
         <div class="todo-content">
          <input type="text" v-model="todo.content" />
         </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>

        
        </div>
      </div>
    </section>
  </main>
</template>
