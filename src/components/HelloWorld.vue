<template>
<div class="container" >
      <div class="flexIcons" >
       <label class="container">
  <input type="checkbox" v-if="completed == true  ? checked=true : checked = false "/>
  <span @click="completed = !completed " class="checkmark"></span>
</label>
        <p v-if="editInput == false" :style="{textDecoration: completed ? 'line-through' : 'none' }"> {{todo.todo}} </p>
        <input type="text" v-if="editInput " v-model="editedTodo " placeholder="edit your todo" class="editInputStyle " />
        <button v-if="editInput " :disabled="editTodo.length < 1 ? true : false" @click="editTodo(todo.id) " class="editTodoBtn" > Edit Todo </button>
      </div>
      <div class="spacedIcons " >
       <Tippy content="remainder"  placement="bottom" >
        <span > <Icon icon="openmoji:alarm-clock" width="30" height="30" /> </span>
       </Tippy>
        <Tippy  :content ="editInput ? 'close' : 'edit' " placement="bottom" >
          <span v-if="editInput == false " @click="editInput = true " ><Icon icon="akar-icons:edit"  width="30" height="30" /> </span>
          <span v-else @click="editInput = false " ><Icon icon="material-symbols-light:close-small" width="30" height="30" /> </span>
        </Tippy>
         <Tippy content="delete"  placement="bottom" >
          <span  @click="deleteTodo" > <Icon icon="heroicons-outline:trash" width="30" height="30" /> </span>
         </Tippy>
      </div>
</div>
</template>

<script>
import {Icon} from '@iconify/vue'
import { Tippy } from 'vue-tippy'
import 'tippy.js/dist/tippy.css'
  export default {
    name: "HelloWorld",
    props: {
      todo: Object,
      completed: Boolean,
      // addTodo: addTodo,
      editedTodo: String,
      editInput: Boolean,
    },
    components: {
   Icon,
    Tippy,
    },
    emits: ['handleDelete'],

    setup(props, {emit}) {

   const deleteTodo = () => {
    emit('handleDelete', props.todo.id)
   }
      return{
       deleteTodo,
      }
    },
  }
</script>

<style>
.flexIcons p{
  font-size: 18px;
}
.container {
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
  background-color: #2196F3;
}

.container input {
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
.container input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.container .checkmark:after {
  left:7px;
  top: 1px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 3px 3px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}
.flexIcons{
  display: flex;
  align-items: center;
  gap: 20px;
}
.flexIcons p{
  color: #fff;
}
.spacedIcons{
  display: flex;
  align-items: center;
  gap: 15px;
}
.spacedIcons span{
  cursor: pointer;
}
</style>