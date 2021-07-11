<template>
  <div class="wrapper">
    <div class="list">
      <input
        type="text"
        class="list__new-todo"
        placeholder="+ Новая задача"
        @keyup.enter="createTodo"
        @blur="$event.target.value = ''"
      >
    </div>
    <div
      class="list"
      v-for="todo of filterTodos.keys()"
      :key="todo.id"
    >
      <div class="list__checkbox">
        <input
          type="checkbox"
          :checked="todo.completed"
          @change="todo.completed = !todo.completed"
        >
      </div>
      <div
        class="list__title"
        :class="{completed: todo.completed}"
      >
        <div
          class="title"
          v-if="!filterTodos.get(todo)"
          @dblclick="startEdit(todo)"
        >
          {{ todo.title }}
        </div>
        <div v-else>
          <input
            type="text"
            class="list__edit-todo"
            :value="todo.title"
            ref="edit"
            @keyup.enter="editTodo"
            @blur="editTodo"
          >
        </div>
      </div>
      <div class="list__buttons">
        <button
          class="btn"
          @click="deleteTodo(todo)"
        >
          <img
            src="@/assets/delete.svg"
            alt="icon-edit"
            class="icon"
          >
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { setItem, getItem } from "@/utils/localStorage";

export default {
  name: "TodoList",

  props: {
    filter: String,
  },

  data: () => ({
    todos: [
      {
        id: 1,
        completed: false,
        title: "Первая задача",
      },
      {
        id: 2,
        completed: false,
        title: "Вторая задача",
      },
      {
        id: 3,
        completed: false,
        title: "Третья задача",
      },
    ],
    editedTodo: null,
  }),

  computed: {
    filterTodos() {
      const map = new Map();
      const regex = new RegExp(this.filter, "i");
      this.todos
        .filter((todo) => todo.title.match(regex))
        .forEach((todo) => {
          todo === this.editedTodo ? map.set(todo, true) : map.set(todo, false);
        });
      return map;
    },
  },

  watch: {
    todos: {
      handler: function (val) {
        setItem("todos", val);
      },
      deep: true,
    },
  },

  created() {
    if (getItem("todos")) {
      this.todos = getItem("todos");
    }
  },

  methods: {
    deleteTodo(todo) {
      this.todos.splice(this.todos.indexOf(todo), 1);
    },
    createTodo({ target }) {
      if (!target.value.trim()) return false;
      this.todos.push({
        id: Date.now(),
        completed: false,
        title: target.value.trim(),
      });
      target.value = "";
    },
    startEdit(todo) {
      this.editedTodo = todo;
      this.$nextTick(function () {
        this.$refs.edit[0].focus();
      });
    },
    editTodo(event) {
      this.editedTodo.title = event.target.value;
      this.editedTodo = null;
    },
  },
};
</script>

<style lang="scss" scoped>
.wrapper {
  padding: 1em;
}

.list {
  background-color: #fff;
  padding: 1em;
  border-radius: 0.5em;
  display: flex;
  align-items: center;

  &:not(:last-child) {
    margin-bottom: 0.5em;
  }

  &__checkbox {
    margin-right: 1em;
  }

  &__title {
    flex: 1 1;
    margin-right: 1em;
  }

  &__new-todo {
    border: none;
    font-family: inherit;
    font-size: inherit;
    color: inherit;
    width: 100%;

    &:focus {
      outline: none;
    }
  }

  &__edit-todo {
    width: 100%;
  }
}

.title {
  cursor: pointer;
}

.completed {
  text-decoration: line-through;
}

.btn {
  width: 30px;
  height: 30px;
  cursor: pointer;
  border: 1px solid transparent;
  border-radius: 0.5em;
  padding: 0.4em;

  &:hover {
    border-color: #ff3232;
    background-color: #ffb4b4;
  }
}
.icon {
  width: 100%;
  height: 100%;
}
</style>