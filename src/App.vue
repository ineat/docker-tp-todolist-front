<template>
  <div id="app">
    <div class="container">
      <div class="add-item">
        <div class="add-icon" @click="addTodo()">
          <i class="fas fa-plus"></i>
        </div>
        <form @submit.prevent="addTodo()">
          <input type="text" placeholder="Add a to-do..." v-model="entry">
        </form>
      </div>
      <div class="todo-list">
        <div class="todo" v-for="(todo, index) in this.todolist" :key="index">
          <div class="todo-checkbox">
            <i class="fas fa-square" @click="closeTodo(todo, index)"></i>
          </div>
          <div class="todo-title">
            {{ todo.description }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'App',
  data() {
    return {
      todolist: [],
      entry: ''
    }
  },
  async beforeCreate() {

    await axios(`${process.env.TODOS_API_URL}/todos`, {
        method: "GET"
      }).then(response => {
        console.log(response);
        this.todolist = response.data.todos;
      })
  },
  methods: {
    async addTodo() {
      if (this.entry !== '') {
        await axios.post(`${process.env.TODOS_API_URL}/todos`, {description: this.entry}).then(response => {
          axios.get(process.env.TODOS_API_URL + response.headers["location"]).then(res => {
            this.todolist.push(res.data);
          })
        })
      }
    },
    async closeTodo(todo, index) {
      await axios.delete(`${process.env.TODOS_API_URL}/todos/${todo._id}`).then(() => {
        this.todolist.splice(index, 1);
      })
    }
  }
}
</script>

<style lang="scss">
@import url('https://use.fontawesome.com/releases/v5.7.2/css/all.css');

body {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  background: linear-gradient(to bottom right, #fc2860, #e82356);
}
#app {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 1rem;
  color: #333;
  width: 100%;
  max-width: 768px;
}
.container {
  padding: 10px;
  width: calc(100% - 20px);
  min-height: calc(100% - 20px);

  @mixin feature-icon($color) {
    grid-column-start: 3;
    grid-column-end: 3;
    display: flex;
    justify-content: center;
    align-items: center;
    color: $color;
    font-size: 1.6rem;
    margin: 0 15px;
    cursor: pointer;
  }

  .add-item {
    display: grid;
    grid-template-columns: 70px auto 70px;
    grid-template-rows: 60px;
    width: 100%;
    height: 60px;
    background: rgba($color: #000000, $alpha: .3);
    border-radius: 5px;
    overflow: hidden;

    .add-icon {
      grid-column-start: 1;
      grid-column-end: 1;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #fff;
      font-size: 2rem;
      cursor: pointer;
    }

    input {
      grid-column-start: 2;
      grid-column-end: 2;
      background: none;
      border: none;
      width: 100%;
      height: 60px;
      color: #fff;
      font-size: 1.6rem;

      &::placeholder {
        color: #fff;
      }

      &:focus {
        outline: none;
      }
    }

    .feature-icon {
      @include feature-icon(#fff);
    }
  }

  .todo-list {
    padding-top: 20px;

    .todo {
      display: grid;
      grid-template-columns: 70px auto 70px;
      grid-template-rows: 60px;
      width: 100%;
      height: 60px;
      margin-bottom: 2px;
      background: rgba($color: #fff, $alpha: .8);
      border-radius: 5px;
      overflow: hidden;
      transition: all .5s linear;

      .todo-checkbox {
        grid-column-start: 1;
        grid-column-end: 1;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 1.6rem;
        color: #e82356;
        cursor: pointer;
      }

      .todo-title {
        grid-column-start: 2;
        grid-column-end: 2;
        display: flex;
        justify-content: flex-start;
        align-items: center;
        font-size: 1.6rem;
      }
    }
  }
}
</style>
