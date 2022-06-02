<template>
  <ToDoList />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import ToDoList from "./components/ToDoList/ToDoList.vue";

export default defineComponent({
  name: "App",
  components: {
    ToDoList,
  },
});
</script>
