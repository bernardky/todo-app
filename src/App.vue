<template>
  <div class="w-full h-screen bg-gray-700 text-center text-white">
    <div class="p-12 max-w-2xl mx-auto"><TodoList /></div>
  </div>
</template>

<script>
import TodoList from "./components/TodoList.vue";
export default {
  components: { TodoList },
};
</script>

<style></style>
