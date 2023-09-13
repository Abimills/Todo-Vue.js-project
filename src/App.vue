<script setup>
import { ref, onMounted, computed, watch } from "vue";
const todos = ref(
  !JSON.parse(localStorage.getItem("todos"))
    ? []
    : JSON.parse(localStorage.getItem("todos"))
);
const name = ref("");
const input_content = ref("");
const updated_content = ref("");
const input_category = ref(null);
const todos_asc = computed(
  () =>
    (todos.value = todos.value.sort((a, b) => {
      return b.createdAt - a.createdAt;
    }))
);
console.log(todos);
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  } else {
    const newTodo = {
      id: Math.floor(Math.random() * 1000),
      content: input_content.value,
      category: input_category.value,
      done: false,
      createAt: Date.now(),
    };
    todos.value = [...todos.value, newTodo];
    input_content.value = "";
    input_category.value = null;
  }
};
const removeTodo = (id) => {
  todos.value = todos.value.filter((todo) => todo.id !== id);
};
const updateTodoTask = (id) => {
  todos.value = todos.value.map((todo) => {
    if (todo.id === id) {
      return { ...todo, done: !todo.done };
    }
    return todo;
  });
};
const editTodo = (id) => {
  todos.value = todos.value.map((todo) => {
    if (todo.id === id) {
      return {
        ...todo,
        content: todo.content,
      };
    } else {
      return todo;
    }
  });
};
watch(todos, (newVal) => {
  localStorage.setItem("todos", JSON.stringify(newVal));
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name Here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list</h4>
        <input
          type="text"
          placeholder="e.g cook food"
          v-model="input_content"
        />
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
        </div>
        <input type="submit" value="Add Todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos" :class="`todo-item ${todo?.done && 'done'}`">
          <label>
            <input
              type="checkbox"
              v-model="todo.done"
              @click="updateTodoTask(todo.id)"
            />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="edit-btn" @click="editTodo(todo.id)">edit</button>
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo.id)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped>
.edit-btn {
  background-color: aquamarine;
  margin-right: 1rem;
}
</style>
