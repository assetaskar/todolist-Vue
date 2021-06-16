<template>
  <header class="header">
    <div class="header__title">
      <h3
        class="title"
        v-if="!editTitle"
        @dblclick="openEditTitle"
      >
        {{ title }}
      </h3>
      <input
        type="text"
        class="title title_width"
        v-else
        v-model.trim="title"
        ref="inputTitle"
        @keyup.enter="closeEditTitle"
        @blur="closeEditTitle"
      >
    </div>

    <div class="header__search">
      <input
        type="text"
        id="search"
        placeholder="Поиск задач"
        maxlength="20"
        class="search"
        :value="value"
        @input="$emit('input', $event.target.value)"
      >
    </div>
  </header>
</template>

<script>
import { setItem, getItem } from "@/utils/localStorage.js";
export default {
  name: "TodoHeader",

  props: {
    value: String,
  },

  data: () => ({
    title: "Двойной клик",
    editTitle: false,
  }),

  created() {
    if (getItem("title")) {
      this.title = getItem("title");
    }
  },

  methods: {
    openEditTitle() {
      this.editTitle = true;
      this.$nextTick(function () {
        if (this.editTitle) {
          this.$refs.inputTitle.focus();
        }
      });
    },
    closeEditTitle() {
      this.editTitle = false;
      setItem("title", this.title);
    },
  },
};
</script>

<style lang="scss" scoped>
.header {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-between;
  background-color: #b4b4b4;
  padding: 1em;
  gap: 1em;

  &__title {
    flex-grow: 1;
  }

  &__search {
    text-align: right;

    @media (max-width: 600px) {
      flex-grow: 1;
      width: 100%;
    }
  }
}

.title {
  font-size: 20px;
  color: inherit;
  font-family: inherit;
  font-weight: 600;
  margin-right: 1em;
  cursor: pointer;

  &_width {
    width: 100%;
  }
}

.search {
  width: calc(100% - 2rem);
  border-radius: 0.5em;
  padding: 0.3em 1em;
  font-size: 14px;
  color: inherit;

  &:focus {
    outline: none;
  }
}
</style>