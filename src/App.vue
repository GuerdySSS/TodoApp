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
      </div>
      <div v-if="todos.length > 0" class="app__wrapper__todo-item app__wrapper__footer">
        <div class="app__wrapper__todo-item__count">{{todos.length}} items left</div>
        <div class="app__wrapper__todo-item__filter">
          <button class="app__wrapper__todo-item__btn" :class="{'app__wrapper__todo-item__btn_active':activeFilter == 321}" @click="changeBtn(0)">All</button>
          <button class="app__wrapper__todo-item__btn" :class="{'app__wrapper__todo-item__btn_active':activeFilter == false, 'app__wrapper__todo-item__btn_disabled':dis2 == 1}" @click="changeBtn(1)">Active</button>
          <button class="app__wrapper__todo-item__btn" :class="{'app__wrapper__todo-item__btn_active':activeFilter == true, 'app__wrapper__todo-item__btn_disabled':dis == 1}" @click="changeBtn(2)">Completed</button>
        </div>
        <button class="app__wrapper__todo-item__btn app__wrapper__todo-item__btn__clear" :class="{'app__wrapper__todo-item__btn_disabled':dis == 1}" @click="clear">Clear completed</button>
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
      activeFilter: 321,
      dis: 0,
      dis2: 0
    }
  },

  mounted() {
    if (localStorage.dis) this.dis = localStorage.dis
    if (localStorage.dis2) this.dis2 = localStorage.dis2
    if (localStorage.getItem('todos')) this.todos = JSON.parse(localStorage.getItem('todos'))
  },

  methods: {
    addTodo() {
      if (this.todo) {
        if (localStorage.getItem('todos')) this.todos = JSON.parse(localStorage.getItem('todos'))
        this.todos.push({
          message: this.todo,
          active: false,
          id: Date.now().toString()
        })
        this.todo = ''
        localStorage.setItem('todos', JSON.stringify(this.todos))
        this.todos.find(el => el.active == true) ? this.dis = 2 : this.dis = 1
        this.todos.find(el => el.active == false) ? this.dis2 = 2 : this.dis2 = 1
        if (this.activeFilter != 321) this.todos = this.todos.filter(el => el.active == this.activeFilter)
        localStorage.dis = this.dis
        localStorage.dis2 = this.dis2
      }
    },

    endedToDo(index) {
      this.todos = JSON.parse(localStorage.getItem('todos'))
      this.todos.forEach((el, i) => {
        if (el.id == index) {
          this.todos[i].active = !this.todos[i].active
        }
      })
      this.disableBtn()
      // if (this.todos.find(el => el.id == index))
      //   el.active == false ? el.active = true : el.active = false
      // this.disableBtn()
      this.filtration()
    },

    deleteItem(index) {
      this.todos = JSON.parse(localStorage.getItem('todos')).filter(el => el.id != index)
      this.disableBtn()
      this.filtration()
    },

    clear() {
      if (this.activeFilter == true) this.activeFilter = 321
      this.todos = JSON.parse(localStorage.getItem('todos')).filter(el => el.active == false)
      this.disableBtn()
    },

    changeBtn(x) {
      this.todos = JSON.parse(localStorage.getItem('todos'))
      this.activeFilter = 321
      if (x != 0) {
        if (x == 1) this.activeFilter = false
        else if (x == 2) this.activeFilter = true
        this.todos = this.todos.filter(el => el.active == this.activeFilter)
      }
    },

    filtration() {
      if (this.activeFilter != 321) this.todos = this.todos.filter(el => el.active == this.activeFilter)
      if (this.todos.length == 0) {
        this.todos = JSON.parse(localStorage.getItem('todos'))
        this.activeFilter = 321
      }
    },

    disableBtn() {
      this.todos.find(el => el.active == true) ? this.dis = 2 : this.dis = 1
      this.todos.find(el => el.active == false) ? this.dis2 = 2 : this.dis2 = 1
      localStorage.dis = this.dis
      localStorage.dis2 = this.dis2
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
    font-size: 20px;
    font-weight: 500;
    align-items: center;
    border-bottom: 1px solid #bcaaa4;

    @media(max-width: 560px) {
      font-size: 15px;
      padding: 10px 15px;
    }
  }
  .app {
    min-height: 100%;
    background-color: #e0e0e0;
    user-select: none;

    @media(max-width: 710px) {
      padding: 0 10px;
    }

    &__header {
      font-size: 60px;
      text-align: center;
      color: #bcaaa4;

      @media(max-width: 560px) {
        font-size: 35px;
      }
    }

    &__wrapper {
      background: white;
      max-width: 700px;
      margin: 0 auto;
      box-shadow: 0 0 10px rgba(0,0,0,0.5);

      &__todo-list {
        max-height: 400px;
        overflow: auto;

        &::-webkit-scrollbar {
          width: 5px;
          background-color: white
        }
        &::-webkit-scrollbar-thumb {
          background-color: #bcaaa4;
          border-radius: 35px;
        }
      }

      &__input {
        border: none;
        width: 100%;
        height: 60px;
        padding: 0px 30px;
        font-size: 20px;
        border-bottom: 1px solid #bcaaa4;
        outline: none;
        color: #616161;

        @media(max-width: 560px) {
          font-size: 15px;
        }

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

        &:last-child {
          border-bottom: none;
        }

        &__text {
          width: auto;

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

          @media(max-width: 560px) {
            font-size: 22px;
          }
        }
        
        &__ended {
          margin-right: 20px;
          cursor: pointer;
          min-height: 35px;
          min-width: 35px;
          border-radius: 50%;
          border: 1px solid #bcaaa4;
          display: flex;
          color: white;
          justify-content: center;
          align-items: center;
          font-size: 25px;

          @media(max-width: 560px) {
            min-height: 25px;
            min-width: 25px;
            font-size: 15px;
          }
          
          &_active {
            color: #43a047;
          }
        }

        &__count {
          @media(max-width: 560px) {
            font-size: 15px;
          }
        }

        &__filter {
          display: flex;
          margin: 0;
          align-items: center;

          @media(max-width: 333px) {
            margin: 5px 0;
          }
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

          @media(max-width: 560px) {
            font-size: 15px;
          }

          &_disabled {
            pointer-events: none;
            cursor: pointer;
            color: #9e9e9e;
          }

          &:last-child {
            margin-right: 0px;
          }

          &_active {
            border-color: #616161;
          }

          &__clear {
            &:hover {
              text-decoration: underline;
            }
          }
        }
      }

      &__footer {
        font-size: 20px;
        border-top: 1px solid #bcaaa4;
        flex-wrap: wrap;
        justify-content: space-between;

        @media(max-width: 465px) {
          justify-content: space-around;
        }
      }
    }
  }
</style>