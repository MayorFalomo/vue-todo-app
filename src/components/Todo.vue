<template>
  <div class="container">
    <div class="flexIcons">
      <label class="labelCon">
        <input
          @click="strikeTodo"
          type="checkbox"
          v-if="todo.completed"
          checked
        />
        <input @click="strikeTodo" type="checkbox" v-else />
        <span class="checkmark"></span>
      </label>
      <p
        v-if="editInput == false"
        :style="{ textDecoration: todo.completed ? 'line-through' : 'none' }"
      >
        {{ todo.todo }}
      </p>
      <input
        type="text"
        v-if="editInput"
        v-model="localAnswer"
        placeholder="edit your todo"
        class="editInputStyle"
      />
      <button
        v-if="editInput"
        :disabled="localAnswer.length < 1 ? true : false"
        @click="editMyTodo"
        class="editTodoBtn"
      >
        Edit Todo
      </button>
    </div>
    <div class="spacedIcons">
      <Tippy content="set a 1min remainder" placement="bottom">
        <span @click="setARemainder">
          <Icon icon="openmoji:alarm-clock" />
        </span>
      </Tippy>
      <Tippy :content="editInput ? 'close' : 'edit'" placement="bottom">
        <span v-if="editInput == false" @click="editInput = true"
          ><Icon icon="akar-icons:edit" />
        </span>
        <span v-else @click="editInput = false"
          ><Icon
            icon="material-symbols-light:close-small"
            width="40"
            height="40"
          />
        </span>
      </Tippy>
      <Tippy content="delete" placement="bottom">
        <span @click="deleteTodo">
          <Icon icon="heroicons-outline:trash" />
        </span>
      </Tippy>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { Icon } from "@iconify/vue";
import { Tippy } from "vue-tippy";
import "tippy.js/dist/tippy.css";
export default {
  name: "Todo",
  props: {
    todo: Object,
    completed: Boolean,
    modelValue: String,
    editInput: Boolean,
  },
  components: {
    Icon,
    Tippy,
  },
  //To emit and to use a props we have to pass them as an argument like this.
  setup(props, { emit }) {
    const localAnswer = ref("");
    const deleteTodo = () => {
      emit("handleDelete", props.todo.id);
    };
    const editedTodo = ref(props.modelValue);

    //Function to emit my id and the edit values, we define a local edit input and emit the function to the parent component
    const editMyTodo = () => {
      emit("editTodo", props.todo.id, localAnswer.value);
    };

    const setARemainder = () => {
      emit("setRemainder", props.todo.id, props.todo.completed);
    };

    const strikeTodo = () => {
      emit("strikeTodo", props.todo.id, props.todo.completed);
    };
    return {
      deleteTodo,
      editMyTodo,
      setARemainder,
      strikeTodo,
      editedTodo,
      localAnswer,
    };
  },
};
</script>

<style>
.container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 30px;
  width: 100%;
  /* border: 2px yellow solid; */
}
.flexIcons {
  display: flex;
  align-items: center;
  gap: 20px;
}
.flexIcons .editInputStyle {
  background-color: #1f2937;
  font-size: 18px;
  line-height: 1.5;
  padding: 10px 15px;
  width: 400px;
  max-width: 80%;
  border-radius: 10px;
  color: white;
  border: none;
  outline: none;
  resize: none;
}

.editTodoBtn {
  padding: 10px 15px;
  font-size: 18px;
  background-color: #1f2937;
  border-radius: 8px;
  color: #fff;
  border: none;
  outline: none;
  cursor: pointer;
}
.flexIcons p {
  color: #fff;
}
.flexIcons p {
  font-size: 18px;
  line-height: 1.5;
}
.labelCon {
  display: block;
  position: relative;
  padding-left: 25px;
  margin-bottom: 12px;
  cursor: pointer;
  font-size: 22px;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.container input:checked ~ .checkmark {
  background-color: #2196f3;
}

.labelCon input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

/* Create a custom checkbox */
.checkmark {
  position: absolute;
  top: 0;
  left: 0;
  height: 20px;
  width: 20px;
  background-color: #eee;
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.labelCon input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.labelCon .checkmark:after {
  left: 7px;
  top: 1px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 3px 3px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}

.spacedIcons {
  display: flex;
  align-items: center;
  gap: 15px;
}
.spacedIcons span {
  cursor: pointer;
  font-size: 24px;
}
</style>
