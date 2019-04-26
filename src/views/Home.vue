<template>
  <div>
    <AddTodo v-on:add-todo="addTodo" />
    <Todos
     v-bind:todos="todos"
     v-on:del-todo="deleteTodo"
     v-on:upd-todo="updateTodo"/>
  </div>
</template>

<script>
import Todos from '../components/Todos';
import AddTodo from '../components/AddTodo';
//import axios from 'axios';
import { constants } from 'crypto';

// const API='https://jsonplaceholder.typicode.com/todos';
const API='http://localhost:5000/api/v1/todos';

export default {
  name: 'Home',
  components: {
    Todos,
    AddTodo
  },
  data() {
    return {
      todos: []
    }
  },
  mounted() {
    if (localStorage.getItem('mystore')) {
      try {
        this.mystore = JSON.parse(localStorage.getItem('mystore'));
      } catch(e) {
        localStorage.removeItem('mystore');
      }
    }
  },
  methods: {
    deleteTodo(id) {
      fetch(`${API}/${id}`, {
        method: 'delete'
      })
        .then(response => {
          response.json().then(data => {
            this.todos = this.todos.filter(todo => todo.id !== id)
          })
        })
        .catch(err => console.log('ERRR'))
    },
    addTodo(newTodo) {
      const { title, completed } = newTodo;
      fetch(API, {
        method: 'post',
        body: JSON.stringify({
          title,
          description: title,
          completed
        }),
        headers: {
          "Content-Type": "application/json"
        }
      })
        .then(response => response.json().then(data => {
          console.log(data);
          this.todos = data
        }))
        .catch(err => console.log(err))
    },
    updateTodo(newTodo) {
      const { title, completed } = newTodo;
      fetch(`${API}/${newTodo.id}`, {
        method: 'put',
        body: JSON.stringify({
          title,
          description: title,
          completed
        }),
        headers: {
          "Content-Type": "application/json"
        }
      })
        .then(response => response.json().then(data => {
          console.log(newTodo);
          this.todos = data
        }))
        .catch(err => console.log(err))
    }
  },
  created() {
    fetch(API)
      .then(response => response.json().then(data => this.todos = data))
      .catch(err => console.log('ERRR'))
  }
}
</script>

<style>
  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }
  body {
    font-family: Arial, Helvetica, sans-serif;
    line-height: 1.4;
  }
  .btn {
    display: inline-block;
    border: none;
    background: #555;
    color: #fff;
    padding: 7px 20px;
    cursor: pointer;
  }
  .btn:hover {
    background: #666;
  }
</style>
