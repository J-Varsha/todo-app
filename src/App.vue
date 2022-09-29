<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const completedTasks = ref([]);

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

watch(
  completedTasks,
  (newVal) => {
    localStorage.setItem("completedTasks", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

const addTodo = () => {
  if (input_content.value.trim() === "") {
    alert("Please eneter a task title");
    return;
  }

  if (input_category.value === null) {
    alert("Please choose task priority");
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
    isComplete: false,
  });

  input_content.value = "";
  input_category.value = null;
};

const editTodo = (epochTime) => {
  console.log(epochTime, "epochTime");
  console.log(todos, "bef edit");
  todos.value.map((item) => {
    if (item.createdAt == epochTime) {      
      item.editable = true;
    }    
  });
  console.log(todos, "after edit");
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

const deleteTodo = (task) => {
  completedTasks.value = completedTasks.value.filter((t) => t !== task);
};

const completeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
  completedTasks.value.push(todo);
};

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
  completedTasks.value =
    JSON.parse(localStorage.getItem("completedTasks")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">My Todo List</h2>
    </section>

    <section class="create-todo">
      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>Add a new task</h4>
        <input
          type="text"
          name="content"
          id="content"
          placeholder="Ex: Watch tutorial"
          v-model="input_content"
        />

        <h4>Set priority</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="High"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>High</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="Medium"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Medium</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category3"
              value="Low"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Low</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3 class="doneTitle">Task To Do</h3>
      <div class="list" id="todo-list">
        <div
          v-for="todo in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <p class="iconBlue">◈</p>
          </label>

          <div class="todo-content">
            <input
              type="text"
              v-model="todo.content"
              :readonly="todo.editable == false"
            />
          </div>

          <div class="todo-content">
            <h3 class="donePriority">
              Priority : <input type="text" v-model="todo.category" readonly />
            </h3>
          </div>

          <div class="actions">
            <button class="complete" @click="completeTodo(todo)">
              Complete
            </button>
          </div>

          <div class="actions">
            <button class="edit" @click="editTodo(todo.createdAt)">Edit</button>
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>

    <section class="todo-list">
      <h3 class="doneTitle">Completed Task</h3>
      <div class="list" id="todo-list">
        <div
          v-for="task in completedTasks"
          :class="`todo-item ${task.done && 'done'}`"
        >
          <label>
            <p class="iconGreen">✔</p>
          </label>

          <div class="todo-content">
            <input type="text" v-model="task.content" readonly />
          </div>

          <div class="todo-content">
            <h3 class="donePriority">
              Priority : <input type="text" v-model="task.category" readonly />
            </h3>
          </div>

          <div class="actions">
            <button class="inVisibleComplete">Complete</button>
          </div>

          <div class="actions">
            <button class="inVisibleEdit">Edit</button>
          </div>

          <div class="actions">
            <button class="delete" @click="deleteTodo(task)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
