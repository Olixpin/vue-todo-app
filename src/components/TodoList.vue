<template>
  <div class="flex flex-col items-start justify-start gap-5 w-full">
    <div class="flex justify-center items-center w-full">
      <img alt="Vue Logo" src="../assets/vue.svg" />
    </div>
    <input
      type="text"
      class="w-full"
      placeholder="Add to do"
      v-model="todo"
      @keyup.enter="addTodo"
    />
    <ul class="w-full flex flex-col gap-3">
      <li
        v-for="(todo, i) in todosFiltered"
        :key="todo.id"
        class="flex justify-between text-xl text-textBody"
      >
        <div class="flex gap-4 items-center justify-center">
          <input
            type="checkbox"
            v-model="todo.completed"
            class="form-checkbox h-5 w-5 transition duration-150 ease-in-out text-primaryMain rounded-md focus:ring-indigo-500 focus:border-indigo-500 border-gray-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-gray-100 disabled:opacity-50 disabled:cursor-not-allowed disabled:ring-0 disabled:border-gray-300 disabled:bg-gray-50 disabled:text-gray-400 disabled:placeholder-gray-300 disabled:ring-offset-gray-100 disabled:focus:ring-offset-gray-100 disabled:focus:ring-indigo-500 disabled:focus:border-indigo-500 disabled:focus:ring-2 disabled:focus:ring-offset-2"
          />
          <div>
            <h1
              v-if="!todo.editing"
              @dblclick="editTodo(todo)"
              class="font-medium"
              :class="{
                'line-through': todo.completed,
                'text-textPlacecholder': todo.completed,
              }"
            >
              {{ todo.title }}
            </h1>
            <input
              v-else
              type="text"
              v-model="todo.title"
              @keyup.enter="updateTodo(todo)"
              @blur="updateTodo(todo)"
              @keyup.esc="cancelEdit(todo)"
              v-focus
            />
          </div>
        </div>
        <button
          class="text-3xl shadow-lg w-8 h-8 flex justify-center items-center rounded-full hover:text-textPlacecholder transition-all"
          @click="removeTodo(i)"
        >
          &times;
        </button>
      </li>
    </ul>

    <div
      class="border-t-[1px] border-b-[1px] border-stokeLight w-full py-4 flex justify-between"
      :class="{
        'border-b-[1px] border-stokeLight': todos.length > 0,
        'border-b-0': todos.length === 0,
      }"
    >
      <div class="flex items-center justify-center gap-4">
        <input
          type="checkbox"
          :checked="!remaining"
          @click="
            todos.forEach((todo) => (todo.completed = $event.target.checked))
          "
          id="alltodos"
          class="form-checkbox h-5 w-5 transition duration-150 ease-in-out text-primaryMain rounded-md focus:ring-indigo-500 focus:border-indigo-500 border-gray-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-gray-100 disabled:opacity-50 disabled:cursor-not-allowed disabled:ring-0 disabled:border-gray-300 disabled:bg-gray-50 disabled:text-gray-400 disabled:placeholder-gray-300 disabled:ring-offset-gray-100 disabled:focus:ring-offset-gray-100 disabled:focus:ring-indigo-500 disabled:focus:border-indigo-500 disabled:focus:ring-2 disabled:focus:ring-offset-2"
        />
        <label for="alltodos">{{
          remaining ? "Check All" : "Uncheck All"
        }}</label>
      </div>
      <div class="">
        <p class="text-textBody">
          {{ remaining }} item{{ remaining > 1 ? "(s)" : "" }} left
        </p>
      </div>
    </div>
    <div class="flex justify-between w-full">
      <div class="flex justify-center items-center gap-1">
        <button
          class="shadow-sm border-[1px] border-textPlacecholder rounded-sm flex justify-center items-center px-2 py-1 hover:text-textPlacecholder transition-all"
          :class="{
            'bg-successMain text-white hover:text-grey100': filter === 'all',
            'text-textBody': todos.length,
          }"
          @click="filter = 'all'"
        >
          All
        </button>
        <button
          class="shadow-sm border-[1px] border-textPlacecholder rounded-sm flex justify-center items-center px-2 py-1 hover:text-textPlacecholder transition-all"
          :class="{
            'bg-successMain text-white hover:text-grey100': filter === 'active',
            'text-textBody': todos.length,
          }"
          @click="filter = 'active'"
        >
          Active
        </button>
        <button
          class="shadow-sm border-[1px] border-textPlacecholder rounded-sm flex justify-center items-center px-2 py-1 hover:text-textPlacecholder transition-all"
          :class="{
            'bg-successMain text-white hover:text-grey100':
              filter === 'completed',
            'text-textBody': todos.length,
          }"
          @click="filter = 'completed'"
        >
          Completed
        </button>
      </div>
      <button
        v-if="showClearCompledButton"
        class="shadow-sm border-[1px] border-textPlacecholder rounded-sm flex justify-center items-center px-2 py-1 hover:text-textPlacecholder transition-all"
        @click="clearCompleted"
      >
        Clear Completed
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "todo-list",

  data() {
    return {
      todo: "",
      newId: 2,
      beforeEditCache: "",
      filter: "all",
      todos: [
        { id: 1, title: "Buy Milk", completed: false, editing: false },
        { id: 2, title: "Wash Dad's Car", completed: false, editing: false },
      ],
    };
  },

  methods: {
    addTodo() {
      if (this.todo.trim() !== "") {
        this.todos.push({
          id: this.newId,
          title: this.todo,
          completed: false,
          editing: false,
        });
      }

      this.todo = "";
      this.newId++;
    },

    directives: {
      focus: {
        mounted(el) {
          el.focus();
        },
      },
    },

    removeTodo(i) {
      this.todos = this.todos.filter((todo, index) => index !== i);
    },

    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;
    },

    updateTodo(todo) {
      if (todo.title.trim() === "") {
        todo.title = this.beforeEditCache;
      }

      todo.editing = false;
    },

    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },

    clearCompleted() {
      this.todos = this.todos.filter((todo) => !todo.completed);
    },
  },

  watch: {
    todos: {
      handler: function (todos) {
        localStorage.setItem("todos", JSON.stringify(todos));
      },
      deep: true,
    },
  },

  created() {
    if (localStorage.getItem("todos"))
      this.todos = JSON.parse(localStorage.getItem("todos"));
  },

  computed: {
    remaining() {
      return this.todos.filter((todo) => !todo.completed).length;
    },

    todosFiltered() {
      if (this.filter === "all") {
        return this.todos;
      } else if (this.filter === "active") {
        return this.todos.filter((todo) => !todo.completed);
      } else if (this.filter === "completed") {
        return this.todos.filter((todo) => todo.completed);
      }

      return this.todos;
    },

    showClearCompledButton() {
      return this.todos.filter((todo) => todo.completed).length > 0;
    },
  },

  mounted() {},
};
</script>
