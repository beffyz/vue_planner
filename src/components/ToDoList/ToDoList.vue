<template>
  <section class="todo-section">
    <div class="todo-box">
      <div class="todo-box__add">
        <input
          class="todo-box__add--input"
          type="text"
          v-model="newTodo.text"
          @keyup.enter="addTask"
          placeholder="Add new task..."
        />
        <button @click="addTask" class="todo-box__add--button">Add</button>
      </div>
      <div>
        <p>
          {{ newTodo.error }}
        </p>
      </div>
      <div class="new-todo">
        <div
          class="new-todo__box"
          :key="todo"
          v-for="todo in toDoList"
          @click="handleCheck(todo)"
        >
          <div>
            <input type="checkbox" v-bind:checked="todo.completed" />
            <span v-if="todo.completed">
              <s>{{ todo.text }}</s>
            </span>
            <span v-if="!todo.completed">{{ todo.text }}</span>
          </div>
          <button @click="removeTask(todo)" class="new-todo__box--button">
            X
          </button>
        </div>
      </div>
      <div class="new-todo__box--filter-box">
        <button
          class="new-todo__box--filter-btn"
          v-for="button in filterButtons"
          @click="filterBy(button.filter)"
          v-bind:class="{ active: button.active, button: true }"
          v-bind:key="button.filter"
        >
          {{ button.text }}
        </button>
      </div>
    </div>
  </section>
</template>

<script lang="ts">
import { defineComponent } from "vue";
export interface Todos {
  error: string;
  id: number;
  text: string;
  completed: boolean;
}
export default defineComponent({
  name: "TodoList",
  data() {
    return {
      newTodo: {} as Todos,
      toDoList: JSON.parse(localStorage.getItem("toDoList") || "[]") as Todos[],
      filterButtons: [
        {
          text: "All",
          filter: "all",
          active: false,
        },
        {
          text: "In progress",
          filter: "active",
          active: false,
        },
        {
          text: "Completed",
          filter: "completed",
          active: false,
        },
      ],
    };
  },
  methods: {
    addTask() {
      if (!this.newTodo.text) {
        this.newTodo.error = "Enter new task!";
        return;
      }
      if (this.newTodo.text) {
        this.newTodo.error = "";
        const todo = {
          ...this.newTodo,
          id: Math.random(),
          completed: false,
        };
        localStorage.setItem(
          "toDoList",
          JSON.stringify([...this.toDoList, todo])
        );

        this.toDoList = [...this.toDoList, todo];
        this.newTodo.text = "";
      }
    },
    removeTask(todo: Todos) {
      this.toDoList = this.toDoList.filter((item) => item.id !== todo.id);
      localStorage.setItem("toDoList", JSON.stringify(this.toDoList));
    },
    handleCheck(todo: Todos) {
      this.toDoList = this.toDoList.map((item) =>
        item.id === todo.id ? { ...item, completed: !item.completed } : item
      );
      localStorage.setItem("toDoList", JSON.stringify(this.toDoList));
    },
    filterBy(filter: string) {
      const allTodos = JSON.parse(
        localStorage.getItem("toDoList") || "[]"
      ) as Todos[];
      if (filter === "all") {
        this.toDoList = allTodos;
        this.filterButtons.forEach((button) => {
          button.active = false;
        });
      } else if (filter === "active") {
        this.toDoList = allTodos.filter((item) => !item.completed);
        this.filterButtons.map((button) => {
          button.active = button.filter === filter;
        });
      } else if (filter === "completed") {
        this.toDoList = allTodos.filter((item) => item.completed);
        this.filterButtons.map((button) => {
          button.active = button.filter === filter;
        });
      }
    },
  },
});
</script>

<style scoped lang="scss" src="./ToDoList.scss" />
