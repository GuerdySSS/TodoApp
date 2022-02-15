<template>
  <div id="app" class="app">
    <h1 class="app__header">Todos</h1>
    <div class="app__wrapper">
      <input class="app__wrapper__input" type="text" placeholder="What needs to be down?" v-model="todo" v-on:keyup.enter="addTodo">
      <div v-if="todos.length < 1" class="app__wrapper__empty">Нет никаких задач</div>
      <div v-else class="app__wrapper__todo-list">
        <div class="app__wrapper__todo-item" v-for="toDo in todos" :key="toDo.id">
          <div class="app__wrapper__todo-item__ended" :class="{'app__wrapper__todo-item__ended_active':toDo.active == true}" @click="endedToDo(toDo.id)">&#10004;</div>
          <div class="app__wrapper__todo-item__text" :class="{'app__wrapper__todo-item__text_active':toDo.active == true}">{{toDo.message}}</div>
          <div class="app__wrapper__todo-item__delete" @click="deleteItem(toDo.id)">&times;</div>
        </div>
        <div class="app__wrapper__todo-item app__wrapper__footer">
          <div class="app__wrapper__todo-item__count">{{todos.length}} items left</div>
          <div class="app__wrapper__todo-item__filter">
            <button class="app__wrapper__todo-item__btn" :class="{'app__wrapper__todo-item__btn_active':activeFilter == 0}" @click="filter(0)">All</button>
            <button class="app__wrapper__todo-item__btn" :class="{'app__wrapper__todo-item__btn_active':activeFilter == 1}" :disabled="!todos.some(el => el.active == false)" @click="filter(1)">Active</button>
            <button class="app__wrapper__todo-item__btn" :class="{'app__wrapper__todo-item__btn_active':activeFilter == 2}" :disabled="!todos.some(el => el.active == true)" @click="filter(2)">Completed</button>
          </div>
          <a class="app__wrapper__todo-item__link" @click="clear">Clear completed</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      todo: '',
      todos: [],
      activeFilter: 0
    }
  },
  mounted() {
    if (localStorage.getItem('todos')) {
      this.todos = JSON.parse(localStorage.getItem('todos'))
    }
  },
  methods: {
    addTodo() {
      if (this.todo) {
        this.todos = JSON.parse(localStorage.getItem('todos'))
        this.todos.push({
          message: this.todo,
          active: false,
          id: Date.now().toString()
        })
        this.todo = ''
        localStorage.setItem('todos', JSON.stringify(this.todos))
        if (this.activeFilter == 1) {
          this.todos = this.todos.filter(el => el.active == false)
        }
        else if (this.activeFilter == 2) {
          this.todos = this.todos.filter(el => el.active == true)
        }
      }
    },
    deleteItem(index) {
      this.todos = JSON.parse(localStorage.getItem('todos'))
      this.todos = this.todos.filter(el => el.id != index)
      localStorage.setItem('todos', JSON.stringify(this.todos))
      if (this.activeFilter == 1) {
        this.todos = this.todos.filter(el => el.active == false)
        if (this.todos.length == 0) {
          this.todos = JSON.parse(localStorage.getItem('todos'))
          this.activeFilter = 0
        }
      }
      else if (this.activeFilter == 2) {
        this.todos = this.todos.filter(el => el.active == true)
        if (this.todos.length == 0) {
          this.activeFilter = 0
          this.todos = JSON.parse(localStorage.getItem('todos'))
        }
      }
    },
    endedToDo(index) {
      this.todos = JSON.parse(localStorage.getItem('todos'))
      this.todos.forEach((el, i) => {
        if (el.id == index) {
          this.todos[i].active == false ? this.todos[i].active = true : this.todos[i].active = false
          localStorage.setItem('todos', JSON.stringify(this.todos))

          if (this.activeFilter == 1) {
            this.todos = this.todos.filter(el => el.active == false)
            if (this.todos.length == 0) {
              this.todos = JSON.parse(localStorage.getItem('todos'))
              this.activeFilter = 0
            }
          }
          else if (this.activeFilter == 2) {
            this.todos = this.todos.filter(el => el.active == true)
            if (this.todos.length == 0) {
              this.todos = JSON.parse(localStorage.getItem('todos'))
              this.activeFilter = 0
            }
          }
        }
      })
    },
    filter(x) {
      this.todos = JSON.parse(localStorage.getItem('todos'))
      let activeBtn = false

      this.activeFilter = 0
      if (x != 0) {
        if (x == 1) {
          this.activeFilter = 1
          activeBtn = true
        } else if (x == 2) {
          activeBtn = false
          this.activeFilter = 2
        }
        this.todos = this.todos.filter(el => el.active != activeBtn)
      }
    },
    clear() {
      if (this.activeFilter == 2)
        this.activeFilter = 0
      this.todos = JSON.parse(localStorage.getItem('todos'))
      this.todos = this.todos.filter(el => el.active == false)
      localStorage.setItem('todos', JSON.stringify(this.todos))
    },
  }
}
</script>

<style scoped lang="scss">
  @font-face {
    font-family: "roboto";
    src: url("./fonts/Roboto-Thin.ttf") format("truetype");
  }
  @mixin todos {
    padding: 10px 30px;
    color: #616161;
    font-size: 25px;
    font-weight: 500;
    align-items: center;
    border-bottom: 1px solid #bcaaa4;
  }
  .app {
    width: 100%;
    min-height: 100%;
    background-color: #e0e0e0;
    user-select: none;

    &__header {
      font-size: 70px;
      text-align: center;
      color: #bcaaa4;
    }

    &__wrapper {
      background: white;
      max-width: 700px;
      margin: 0 auto;
      box-shadow: 0 0 10px rgba(0,0,0,0.5);

      &__input {
        border: none;
        width: 100%;
        height: 60px;
        padding: 0px 30px;
        font-size: 20px;
        border-bottom: 1px solid #bcaaa4;
        outline: none;
        color: #616161;

        &::placeholder {
          font-style: italic;
        }
      }

      &__empty {
        @include todos;
        text-align: center;
        padding: 17px 30px;
      }

      &__todo-item {
        @include todos;
        display: flex;

        &__text {
          position: relative;
          width: 540px;

          &_active {
            text-decoration: line-through;
            color: #cfd8dc;
          }
        }

        &__delete {
          color: #bcaaa4;
          margin-left: auto;
          cursor: pointer;
          font-size: 35px;
        }
        
        &__ended {
          margin-right: 20px;
          cursor: pointer;
          height: 40px;
          width: 40px;
          border-radius: 50%;
          border: 1px solid #bcaaa4;
          text-align: center;
          color: white;
          
          &_active {
            color: #43a047;
          }
        }

        &__filter {
          display: flex;
          margin-left: auto;
          margin-right: auto;
          align-items: center;
        }

        &__btn {
          margin-right: 20px;
          cursor: pointer;
          padding: 5px;
          border-radius: 5px;
          background-color: white;
          border: none;
          border: 1px solid white;
          font-size: 20px;

          &:last-child {
            margin-right: 0px;
          }

          &_active {
            border-color: #616161;
          }
        }

        &__link {
          cursor: pointer;
          
          &:hover {
            text-decoration: underline;
          }
        }
      }

      &__footer {
        font-size: 20px;
      }
    }
  }
</style>