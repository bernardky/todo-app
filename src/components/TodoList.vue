<template>
  <div>
    <div class="my-2">
      <input
        class="p-2 w-full rounded-md text-gray-700 outline-none"
        type="text"
        placeholder="What needs to be done?"
        v-model="newTodo"
        @keyup.enter="addTodo"
      />
    </div>
    <div class="my-6">
      <button
        class="px-4 py-2"
        :class="[filter == 'all' ? 'border-b-2' : 'opacity-20']"
        @click="filter = 'all'"
      >
        All
      </button>
      <button
        class="px-4 py-2"
        :class="[filter == 'active' ? 'border-b-2' : 'opacity-20']"
        @click="filter = 'active'"
      >
        Active
      </button>
      <button
        class="px-4 py-2"
        :class="[filter == 'completed' ? 'border-b-2' : 'opacity-20']"
        @click="filter = 'completed'"
      >
        Completed
      </button>
    </div>
    <div
      class="flex justify-between items-start p-2 border-b-2 border-gray-400"
      v-for="(todo, index) in todosFiltered"
      :key="todo.id"
    >
      <div
        :class="[
          todo.importance == 2 ? ' border-red-400' : '',
          todo.importance == 3 ? ' border-yellow-400' : '',
          todo.importance == 4 ? ' border-green-400' : '',
          todo.completed ? 'line-through opacity-30' : '',
          'border-l-8 pl-4 border-solid',
        ]"
      >
        {{ todo.title }}
      </div>
      <div>
        <select
          @change="selectedImportance(index, $event)"
          name="importance"
          class="bg-gray-700 text-xs text-right"
        >
          <option disabled selected value style="display: none"></option>
          <option disabled value="1">Select One</option>
          <option value="2">Very Important</option>
          <option value="3">Important</option>
          <option value="4">Less Important</option>
        </select>
        <i
          class="fa-solid fa-check px-4 hover:text-green-400"
          @click="completeTodo(index)"
        ></i>

        <i
          class="fas fa-times cursor-pointer hover:text-red-400"
          @click="removeTodo(index)"
        ></i>
      </div>
    </div>

    <div
      class="flex justify-between px-2 py-4 text-xs text-gray-400"
      v-if="filter != 'completed'"
    >
      <p
        class="cursor-pointer hover:text-green-400"
        @click="completeAllTodos()"
      >
        Mark All Complete
      </p>
      <div class="flex">
        <p class="px-4 border-l-8 border-red-400 border-solid">
          Very Important
        </p>
        <p class="px-4 border-l-8 border-yellow-400 border-solid">Important</p>
        <p class="px-4 border-l-8 border-green-400 border-solid">
          Less Important
        </p>
      </div>
      <p class="italic">{{ remainingTodos }} {{ itemString }} left</p>
    </div>
    <div class="flex justify-end px-2 py-4 text-xs text-gray-400" v-else>
      <p
        class="cursor-pointer hover:text-red-400"
        @click="clearCompletedTodos()"
      >
        Clear All
      </p>
    </div>
  </div>
</template>

<script>
import { v4 as uuidv4 } from "uuid";
export default {
  name: "todo-list",
  data() {
    return {
      newTodo: "",
      filter: "all",
      todos: [],
    };
  },

  methods: {
    addTodo() {
      if (this.newTodo != "") {
        this.todos.push({
          id: uuidv4(),
          title: this.newTodo,
          completed: false,
          importance: 1,
        });

        this.newTodo = "";
        console.log(this.todos[this.todos.length - 1].id);
      } else {
        alert("No todo entered!");
      }
      this.todos = this.todos.sort((a, b) => a.importance - b.importance);
      this.saveTodos();
    },

    completeTodo(index) {
      this.todos[index].completed = !this.todos[index].completed;
      this.saveTodos();
    },

    removeTodo(index) {
      this.todos.splice(index, 1);
      this.saveTodos();
    },

    completeAllTodos() {
      this.todos.forEach((todo) => (todo.completed = true));
      this.saveTodos();
    },

    clearCompletedTodos() {
      this.todos = this.todos.filter((todo) => !todo.completed);
      this.saveTodos();
    },
    selectedImportance(index, e) {
      this.todos[index].importance = e.target.value;
      this.todos = this.todos.sort((a, b) => a.importance - b.importance);
      this.saveTodos();
    },

    //saving to local storage
    saveTodos() {
      const parsed = JSON.stringify(this.todos);
      localStorage.setItem("todos", parsed);
    },
  },

  computed: {
    remainingTodos() {
      return this.todos.filter((todo) => !todo.completed).length;
    },
    itemString() {
      return this.todos.filter((todo) => !todo.completed).length === 0
        ? "item"
        : "items";
    },

    todosFiltered() {
      if (this.filter == "all") {
        return this.todos;
      } else if (this.filter == "active") {
        return this.todos.filter((todo) => !todo.completed);
      } else if (this.filter == "completed") {
        return this.todos.filter((todo) => todo.completed);
      }
    },
  },

  mounted() {
    if (localStorage.getItem("todos")) {
      try {
        this.todos = JSON.parse(localStorage.getItem("todos"));
      } catch (e) {
        localStorage.removeItem("todos");
      }
    }
  },
};
</script>

<style></style>
