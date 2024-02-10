<script>
import { ref, computed, onMounted } from "vue";
import { Icon } from "@iconify/vue";
import { Tippy } from "vue-tippy";
import "tippy.js/dist/tippy.css";
import Todo from ".//components/Todo.vue";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
export default {
  components: {
    Icon,
    Tippy,
    Todo,
  },
  setup() {
    //First i defined the array going to be holding the todo and all other thing's i'd need in my object
    const todosArray = ref([]);
    const todo = ref("");
    const date = ref("");
    const editedTodo = ref("");
    const completed = ref(false);
    const editInput = ref(false);
    const getClicked = ref("All");

    const addTodo = () => {
      const newTodo = {
        id: Date.now(),
        todo: todo.value,
        date: new Date(),
        completed: completed.value,
      };
      //spread over todosArray
      const newTodosArray = [newTodo, ...todosArray.value];
      todosArray.value = newTodosArray;
      localStorage.setItem("todos", JSON.stringify(todosArray.value));
      todo.value = "";
    };

    //Function to delete a todo
    const deleteTodo = (id) => {
      const newTodosArray = todosArray.value.filter((todo) => todo.id !== id);
      todosArray.value = newTodosArray;
      localStorage.setItem("todos", JSON.stringify(todosArray.value));
      toast.success("Todo deleted successfully", {
        autoClose: 3000,
      });
    };

    //Function to edit a todo
    const editTodo = (id, editedTodo) => {
      editInput.value = true;
      const newTodosArray = todosArray.value.map((todo) => {
        if (todo.id == id) {
          const editedObject = {
            todo: editedTodo,
          };
          editInput.value = false;
          toast.success("todo updated !", {
            autoClose: 3000,
          });
          return { ...todo, ...editedObject };
        }
        return todo;
      });
      todosArray.value = newTodosArray;
    };

    const setRemainder = (id, complete) => {
      setTimeout(() => {
        const newTodosArray = todosArray.value.map((todo) => {
          if (todo.id == id) {
            const editedObject = {
              completed: !complete,
            };
            toast.success("Hey!! your Todo is completed!", {
              autoClose: 5000,
            });
            return { ...todo, ...editedObject };
          }
          return todo;
        });
        return (todosArray.value = newTodosArray);
      }, 1 * 60 * 1000);
    };

    const strikeTodo = (id, completedTodo) => {
      console.log(completedTodo, "completed Todo");
      const newTodosArray = todosArray.value.map((todo) => {
        if (todo.id == id) {
          return { ...todo, completed: !completedTodo };
        }
        return todo;
      });
      todosArray.value = newTodosArray;
    };

    const clicked = (value) => {
      getClicked.value = value;
    };

    //Using Computed property
    const compute = computed(() => {
      if (getClicked.value == "Completed") {
        return todosArray.value.filter((todo) => todo.completed == true);
      } else if (getClicked.value == "Pending") {
        return todosArray.value.filter((todo) => todo.completed == false);
      } else {
        return todosArray.value;
      }
    });

    //OnMounted get the items from localStorage and set it to todosArray
    onMounted(() => {
      todosArray.value = JSON.parse(localStorage.getItem("todos")) || [];
    });

    return {
      todosArray,
      todo,
      date,
      completed,
      addTodo,
      todosArray,
      deleteTodo,
      editedTodo,
      editTodo,
      editInput,
      setRemainder,
      compute,
      getClicked,
      clicked,
      strikeTodo,
    };
  },
};
</script>

<template>
  <div class="TodoApp">
    <div class="centeredTodo">
      <h1>Todo App</h1>
      <form class="formClass" @submit.prevent="addTodo">
        <textarea placeholder="enter your todo " v-model="todo" />
        <button
          :disabled="todo.length < 1 ? true : false"
          :style="{ cursor: todo.length < 1 ? 'not-allowed' : 'pointer' }"
          type="submit"
        >
          Add a Todo
        </button>
      </form>
      <ul>
        <li
          @click="clicked('All')"
          :style="{
            borderBottom:
              getClicked == 'All' ? '3px rgba(20, 157, 206, 0.601) solid ' : '',
          }"
        >
          All
        </li>
        <li
          @click="clicked('Pending')"
          :style="{
            borderBottom:
              getClicked == 'Pending'
                ? '3px rgba(20, 157, 206, 0.601) solid '
                : '',
          }"
        >
          Pending
        </li>
        <li
          @click="clicked('Completed')"
          :style="{
            borderBottom:
              getClicked == 'Completed'
                ? '3px rgba(20, 157, 206, 0.601) solid '
                : '',
          }"
        >
          Completed
        </li>
      </ul>
      <div class="todoArea">
        <div class="mappedTodo" v-for="todo in compute" :key="todo.id">
          <Todo
            :todo="todo"
            :completed="completed"
            v-model="editedTodo"
            :editInput="editInput"
            @handleDelete="deleteTodo"
            @editTodo:value="editTodo"
            @setRemainder="setRemainder"
            @strikeTodo="strikeTodo"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.TodoApp {
  background-color: black;
  color: white;
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
  font-family: "Cabin";
}
.centeredTodo {
  width: 60%;
}
.centeredTodo h1 {
  text-align: center;
}
.todoArea {
  display: flex;
  flex-direction: column;
  gap: 20px;
}
.mappedTodo {
  width: 100%;
}
form {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
  margin: 20px auto;
}
form textarea {
  background-color: #1f2937;
  font-size: 18px;
  line-height: 1.5;
  padding: 10px 15px;
  width: 600px;
  max-width: 80%;
  border-radius: 10px;
  color: white;
  border: none;
  outline: none;
  resize: none;
}

form textarea::-webkit-scrollbar {
  width: 0;
}
form textarea::placeholder {
  font-size: 20px;
  /* color: white; */
  padding: 5px;
}
form button {
  padding: 7px 10px;
  font-size: 18px;
  border-radius: 8px;
  cursor: pointer;
  width: 130px;
  background: linear-gradient(0deg, rgba(0, 0, 0, 0.2), rgba(0, 0, 0, 0.2)),
    linear-gradient(
      90deg,
      #ec4899 0%,
      #8b5cf6 100%,
      rgba(255, 255, 255, 0) 100%
    );
  color: white;
}

ul {
  display: flex;
  align-items: center;
  justify-content: space-around;
  gap: 20px;
  margin: 30px auto;
}
li {
  list-style: none;
  font-size: 18px;
  cursor: pointer;
  padding: 10px 15px;
}

@media (max-width: 1100px) {
  .centeredTodo {
    width: 95%;
  }
  form {
    display: flex;
    flex-direction: column;
  }
  form textarea {
    max-width: 100%;
  }
}
</style>
