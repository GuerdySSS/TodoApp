<template>
  <div id="app" class="app">
    <h1 class="app__header">Todos</h1>
    <div class="app__wrapper">
      <input class="app__wrapper__input" type="text" placeholder="What needs to be down?" v-model="todo" v-on:keyup.enter="addTodo">
      <div v-if="todos.length < 1" class="app__wrapper__empty">Нет никаких задач</div>
      <div v-else class="app__wrapper__todo-list">
        <div class="app__wrapper__todo-item" v-for="toDo in todos" :key="toDo.id">
          <div class="app__wrapper__todo-item__ended" @click="endedToDo(toDo.id)">&#10004;</div>
          <div class="app__wrapper__todo-item__text">{{toDo.message}}</div>
          <div class="app__wrapper__todo-item__delete" @click="deleteItem(toDo.id)">&times;</div>
        </div>
        <div class="app__wrapper__todo-item app__wrapper__footer">
          <div class="app__wrapper__todo-item__count">{{todos.length}} items left</div>
          <div class="app__wrapper__todo-item__filter">
            <button class="app__wrapper__todo-item__btn app__wrapper__todo-item__btn_active" @click="filter(0)">All</button>
            <button class="app__wrapper__todo-item__btn" @click="filter(1)">Active</button>
            <button class="app__wrapper__todo-item__btn" @click="filter(2)">Completed</button>
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
      todos: []
    }
  },
  methods: {
    addTodo() {
      if (this.todo) {
        this.todos.push({
        message: this.todo,
        active: false,
        id: Date.now().toString()
      })
      this.todo = ''
      }
    },
    deleteItem(index) {
      this.todos.forEach((el, i) => {
        if (el.id == index)
          this.todos.splice(i, 1)
      })
    },
    endedToDo(index) {
      this.todos.forEach((el, i) => {
        if (el.id == index) {
          let text = document.getElementsByClassName('app__wrapper__todo-item__text')[i]
          let btnActive = document.getElementsByClassName('app__wrapper__todo-item__ended')[i]
          text.classList.toggle('app__wrapper__todo-item__text_active')
          btnActive.classList.toggle('app__wrapper__todo-item__ended_active')
          this.todos[i].active == false ? this.todos[i].active = true : this.todos[i].active = false
        }
      })
    },
    filter(x) {
      let activeBtn = false
      for(let i=0; i<3; i++)
        document.getElementsByClassName('app__wrapper__todo-item__btn')[i].classList.remove('app__wrapper__todo-item__btn_active')
      let btn = document.getElementsByClassName('app__wrapper__todo-item__btn')[x]
      btn.classList.add('app__wrapper__todo-item__btn_active')

      this.todos.forEach((el, i) => {
        document.getElementsByClassName('app__wrapper__todo-item')[i].style.display = 'flex'
      })

      if (x != 0) {
        x == 1 ? activeBtn = true : activeBtn = false

        this.todos.forEach((el, i) => {
          if (el.active == activeBtn) {
            document.getElementsByClassName('app__wrapper__todo-item')[i].style.display = 'none'
          }
        })
      }
    },
    clear() {
      this.todos.forEach((el, i) => {
        if (el.active == true)
          this.todos.splice(i, 1)
      })
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
    height: 100%;
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