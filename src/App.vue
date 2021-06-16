<template>
  <div id="app">
    <TodoHeader v-model.trim="search" />
    <TodoList :filter="search" />
  </div>
</template>

<script>
import TodoHeader from "./components/TodoHeader";
import TodoList from "./components/TodoList";
import { setItem, getItem } from "./utils/localStorage";
export default {
  name: "App",

  components: {
    TodoHeader,
    TodoList,
  },

  data: () => ({
    search: "",
  }),

  watch: {
    search: function (val) {
      setItem("search", val);
    },
  },

  created() {
    if (getItem("search")) {
      this.search = getItem("search");
    }
  },
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
}
body {
  background-color: #ccc;
}
#app {
  color: #1f2c3a;
}
</style>
