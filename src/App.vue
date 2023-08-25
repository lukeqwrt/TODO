<script setup>
import { computed, ref } from 'vue';
import Todo from './components/Todo.vue'

// import vue use
import { useDark, useToggle } from '@vueuse/core';
const isDark = useDark()
const toggleDark = useToggle(isDark)


// import images
import sun from './assets/images/icon-sun.svg'
import moon from './assets/images/icon-moon.svg'

import headerimgLight from './assets/images/bg-desktop-light.jpg'
import headerimgDark from './assets/images/bg-desktop-dark.jpg'

// data
const todos = ref([])
const input_content = ref('')
const done = ref(false)
const del = ref()
const filters = ref(['All','Active','Completed'])
const activeFilter = ref('All')

// methods:
const todo_submit = () => {
  if(input_content.value.trim() === ''){
    return
  }
  todos.value.push({
    content: input_content.value,
    done:done.value,
    createdAt: new Date().getTime()
  })
  done.value = false
  input_content.value = ''
}

const deleteTodo = (index) => {
  const findIndex = todos.value.findIndex((i,todoIndex) => {
    return index === todoIndex
  })
  del.value = findIndex
  todos.value.splice(findIndex, 1)  
  del.value = null
}
const filterClicked = (filter) => {
  activeFilter.value = filter
}
const clearCompleted = () => {
  const removeDone = todos.value.filter((todo) => todo.done !== true)
  todos.value = removeDone
}
// computed
const activeState = computed(() => {
  return todos.value.filter((todo) => todo.done === false)
})
const completedState = computed(() => {
  return todos.value.filter((todo) => todo.done === true)
})
</script>

<template>
  <div class="main" :class="isDark ? 'dark' : 'light'">
    <img :src="isDark ? headerimgDark : headerimgLight" alt="" class="header">

    <div class="todo_container">
      <div class="todo_header">
        <h3>TODO</h3>
        <div class="theme_toggle">
          <img class="themeimg" @click="toggleDark()" :src="isDark ? sun : moon" alt="">
        </div>
      </div>
      <form @submit.prevent="todo_submit" class="form">
        <div class="check">
          <label>
            <input v-model="done" class="checkbox" type="checkbox">
            <div class="bubble" :class="{ 'todocheck' : done }">
              <img src="./assets/images/icon-check.svg" alt="">
            </div>
          </label>
        </div>
        <input class="input" type="text" v-model="input_content">
        <input class="submit" type="submit" value="submit">
      </form>

      <div class="todos_list">
        <Todo v-if="activeFilter === 'All'" @delete="deleteTodo" :todos="todos" />
        <Todo v-if="activeFilter === 'Active'" @delete="deleteTodo" :todos="activeState" />
        <Todo v-if="activeFilter === 'Completed'" @delete="deleteTodo" :todos="completedState" />
        <div class="todo_footer">
          <div class="items">
            {{ todos.length }} items left
          </div>
          <ul class="filter">
            <li @click="filterClicked(filter)" :class="{'active' : activeFilter === filter}" v-for="filter in filters" :key="filter">
              {{filter}}
            </li>
          </ul>
        <div @click="clearCompleted()" class="clear">
          Clear Completed
        </div>
        </div>
      </div>
      <ul class="filter mobile">
            <li @click="filterClicked(filter)" :class="{'active' : activeFilter === filter}" v-for="filter in filters" :key="filter">
              {{filter}}
            </li>
          </ul>
    </div>
  </div>
</template>

<style lang="scss">
  .main{
    height: 100vh;
    display: flex;
    justify-content: center;
    position: relative;
    
    font-family: 'Inter', sans-serif;
    font-family: 'Josefin Sans', sans-serif;
    &.dark{
      background: hsl(235, 21%, 11%);
      .header{
      position: absolute;
      top: 0;
      left: 0;
      height: 35%;
      width: 100%;
      object-fit: cover;
      @media screen and (max-width: 768px) {
        height: 30%;
      }
            
    }

    .todo_container{
      position: relative;
      z-index: 9999;
      width: 100%;
      max-width: 600px;
      margin-top: 80px;
      padding: 0 30px;
      .todo_header{
        padding: 15px 0;
        color: #fff;
        letter-spacing: 20px;
        font-size: 40px;
        font-weight: 400;
        display: flex;
        justify-content: space-between;
        
        .theme_toggle{
          .themeimg{
            height: auto;
            width: 30px;
            cursor: pointer;
          }
        }
      }
      .form{
        height: 65px;
        background: hsl(235, 24%, 19%);
        position: relative;
        display: flex;
        align-items: center;
        .check{
          margin: 0 20px;
          label{
            .checkbox{
              display: none;
            }
            .bubble{
                border: 2px solid hsl(233, 14%, 35%);
                height:30px;
                width:30px;
                border-radius: 100px;
                cursor: pointer;
                display: flex;
                justify-content: center;
                align-items: center;
                background: linear-gradient(to bottom right, hsla(192, 100%, 67%, 0), hsla(280, 87%, 65%, 0));
                transition: all 0.3s ease;
                img{
                  height: auto;
                  width: 14px;
                  visibility: hidden;
                  opacity: 0;
                }
                &.todocheck{
                  transition: all 0.3s ease;
                  background: linear-gradient(to bottom right, hsl(192, 100%, 67%), hsl(280, 87%, 65%));
                  img{
                    visibility:visible;
                  opacity: 1;
                    height: auto;
                    width: 14px;
                    pointer-events: none;
                  }
                }
            }
          }
        }
        .input{
          background: none;
          height: 100%;
          width: 100%;
          border: 0;
          outline: none;
          color: hsl(234, 39%, 85%);
          font-weight: 400;
          font-size: 21px;
          font-family: 'Josefin Sans', sans-serif;
        }
        .submit{
          position: absolute;
          top: 50%;
          right: 20px;
          transform: translate(0, -50%);
          border: 0;
          outline: none;
          background:hsl(235, 21%, 11%) ;
          color: #fff;
          width: 90px;
          height: 40px;
          font-family: 'Josefin Sans', sans-serif;
          font-size: 18px;
          cursor: pointer;
        }
      }
      .todos_list{
        margin-top: 20px;
                      
        .todo_item{
          height: 65px;
          background: hsl(235, 24%, 19%);
          display: flex;
          align-items: center;
          border-bottom: 1px solid hsl(237, 14%, 26%);
          animation: move 0.3s ease-in-out;
          transition: 0.3s ease-in-out;
          @keyframes move {
            0%{
              opacity: 0;
              transform: translate(-20px,0);
            }
            100%{
              opacity: 1;
              transform: translate(0,0);
            }
          }
          .check{
            margin: 0 20px;
            label{
              .checkbox{
                display: none;
              }
              .bubble{
                border: 2px solid  hsl(233, 14%, 35%);
                height:30px;
                width:30px;
                border-radius: 100px;
                cursor: pointer;
                display: flex;
                justify-content: center;
                align-items: center;
                background: linear-gradient(to bottom right, hsla(192, 100%, 67%, 0), hsla(280, 87%, 65%, 0));
                transition: all 0.3s ease;
                img{
                  height: auto;
                  width: 14px;
                  visibility: hidden;
                  opacity: 0;
                }
                &.todocheck{
                  transition: all 0.3s ease;
                  background: linear-gradient(to bottom right, hsl(192, 100%, 67%), hsl(280, 87%, 65%));
                  img{
                    visibility:visible;
                  opacity: 1;
                    height: auto;
                    width: 14px;
                    pointer-events: none;
                  }
                }
              }
            }
          }
          input{
            height: 100%;
            width: 100%;
            color: hsl(234, 39%, 85%);
            font-size: 21px;
            font-weight: 400;
            padding-right: 15px;
            background: none;
            outline: none;
            border: none;
            font-family: 'Josefin Sans', sans-serif;
            &.cross{
              text-decoration: line-through;
              pointer-events: none;
              color: hsl(234, 11%, 52%);
            }
          }
          .delete{
            margin: 0 20px 0 5px;
            cursor: pointer;
          }
        }
        .todo_footer{
          display: flex;
          justify-content: space-between;
          align-items: center;
          padding: 20px 20px;
          background: hsl(235, 24%, 19%);
          color: hsl(234, 11%, 52%);
          .items{
            font-size: 16px;
            cursor: pointer;
            transition: 0.3s ease-in-out;
            &:hover{
              color: hsl(234, 39%, 85%);
            }
          }
          .filter{
            display: flex;
            gap: 25px;
            @media screen and (max-width: 768px) {
              display: none;
            }
            
            li{
              cursor: pointer;
              list-style: none;
              font-size: 18px;
              transition: 0.3s ease-in-out;
              &:hover{
                color: hsl(234, 39%, 85%);
              }
              &.active{
                color: hsl(220, 98%, 61%);
              }
            }
          }
          .clear{
            cursor: pointer;
            transition: 0.3s ease-in-out;
            &:hover{
              color: hsl(234, 39%, 85%);
            }
          }
        }
      }

      .mobile{
            margin-top: 20px;
            padding: 20px;
            background: hsl(235, 24%, 19%);
            display: none;
            justify-content: center;
            gap: 20px;
            
            @media screen and (max-width: 768px) {
              display:flex;
            }
            li{
              cursor: pointer;
              list-style: none;
              font-size: 18px;
              transition: 0.3s ease-in-out;
              color: hsl(234, 11%, 52%);
              &:hover{
                color: hsl(234, 39%, 85%);
              }
              &.active{
                color: hsl(220, 98%, 61%);
              }
            }
          }
    }
    }
    &.light{
      background: hsl(236, 33%, 92%);

      .header{
      position: absolute;
      top: 0;
      left: 0;
      height: 35%;
      width: 100%;
      @media screen and (max-width: 768px) {
        height: 30%;
      }
    }

    .todo_container{
      position: relative;
      z-index: 9999;
      width: 100%;
      max-width: 600px;
      margin-top: 80px;
      padding: 0 30px;
      .todo_header{
        padding: 15px 0;
        color: #fff;
        letter-spacing: 20px;
        font-size: 40px;
        font-weight: 400;
        display: flex;
        justify-content: space-between;

        .theme_toggle{
          .themeimg{
            height: auto;
            width: 30px;
            cursor: pointer;
          }
        }
      }
      .form{
        height: 65px;
        background: hsl(0, 0%, 98%);
        position: relative;
        display: flex;
        align-items: center;
        box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
        .check{
          margin: 0 20px;
          label{
            .checkbox{
              display: none;
            }
            .bubble{
                border: 2px solid hsl(236, 33%, 92%);
                height:30px;
                width:30px;
                border-radius: 100px;
                cursor: pointer;
                display: flex;
                justify-content: center;
                align-items: center;
                background: linear-gradient(to bottom right, hsla(192, 100%, 67%, 0), hsla(280, 87%, 65%, 0));
                transition: all 0.3s ease;
                img{
                  height: auto;
                  width: 14px;
                  visibility: hidden;
                  opacity: 0;
                }
                &.todocheck{
                  transition: all 0.3s ease;
                  background: linear-gradient(to bottom right, hsl(192, 100%, 67%), hsl(280, 87%, 65%));
                  img{
                    visibility:visible;
                  opacity: 1;
                    height: auto;
                    width: 14px;
                    pointer-events: none;
                  }
                }
            }
          }
        }
        .input{
          background: none;
          height: 100%;
          width: 100%;
          border: 0;
          outline: none;
          color:  hsl(235, 19%, 35%);
          font-weight: 400;
          font-size: 21px;
          font-family: 'Josefin Sans', sans-serif;
        }
        .submit{
          position: absolute;
          top: 50%;
          right: 20px;
          transform: translate(0, -50%);
          border: 0;
          outline: none;
          background:hsl(236, 33%, 92%);
          color:  hsl(240, 6%, 23%);
          width: 90px;
          height: 40px;
          font-family: 'Josefin Sans', sans-serif;
          font-size: 18px;
          cursor: pointer;
        }
      }
      .todos_list{
        margin-top: 20px;
        transition: 0.3s ease-in-out;
        .todo_item{
          height: 65px;
          background: hsl(0, 0%, 98%);
          display: flex;
          align-items: center;
          border-bottom: 1px solid hsl(236, 33%, 92%);
          animation: move 0.3s ease-in-out;
          transition: 0.3s ease-in-out;
          box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
          .check{
            margin: 0 20px;
            label{
              .checkbox{
                display: none;
              }
              .bubble{
                border: 2px solid hsl(236, 33%, 92%);
                height:30px;
                width:30px;
                border-radius: 100px;
                cursor: pointer;
                display: flex;
                justify-content: center;
                align-items: center;
                background: linear-gradient(to bottom right, hsla(192, 100%, 67%, 0), hsla(280, 87%, 65%, 0));
                transition: all 0.3s ease;
                img{
                  height: auto;
                  width: 14px;
                  visibility: hidden;
                  opacity: 0;
                }
                &.todocheck{
                  transition: all 0.3s ease;
                  background: linear-gradient(to bottom right, hsl(192, 100%, 67%), hsl(280, 87%, 65%));
                  img{
                    visibility:visible;
                  opacity: 1;
                    height: auto;
                    width: 14px;
                    pointer-events: none;
                  }
                }
              }
            }
          }
          @keyframes move {
            0%{
              opacity: 0;
              transform: translate(-20px,0);
            }
            100%{
              opacity: 1;
              transform: translate(0,0);
            }
          }
          input{
            font-family: 'Josefin Sans', sans-serif;
            height: 100%;
            width: 100%;
            color:  hsl(235, 19%, 35%);
            font-size: 21px;
            font-weight: 400;
            padding-right: 15px;
            background: none;
            outline: none;
            border: none;

            &.cross{
              text-decoration: line-through;
              pointer-events: none;
            }
          }
          .delete{
            margin: 0 20px 0 5px;
            cursor: pointer;
          }
        }
        .todo_footer{
          display: flex;
          justify-content: space-between;
          align-items: center;
          padding: 20px 20px;
          background: hsl(0, 0%, 98%);
          font-family: 'Josefin Sans', sans-serif;
          color: hsl(234, 11%, 52%);
          box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
          .items{
            font-size: 16px;
            cursor: pointer;
            transition: 0.3s ease-in-out;
            &:hover{
              color:  hsl(235, 19%, 35%);
            }
          }
          .filter{
            display: flex;
            gap: 15px;
            @media screen and (max-width: 768px) {
              display: none;
            }
            li{
              cursor: pointer;
              list-style: none;
              font-size: 18px;
              transition: 0.3s ease-in-out;
              &:hover{
                color:  hsl(235, 19%, 35%);
              }
              &.active{
                color: hsl(220, 98%, 61%);
              }
            }
          }
          .clear{
            cursor: pointer;
            transition: 0.3s ease-in-out;
            &:hover{
              color:  hsl(235, 19%, 35%);
            }
          }
        }
      }
      .mobile{
             background: hsl(0, 0%, 98%);
             color: hsl(234, 11%, 52%);
            box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
            margin-top: 20px;
            padding: 20px;
            display: none;
            justify-content: center;
            gap: 20px;
            
            @media screen and (max-width: 768px) {
              display:flex;
            }
            li{
              cursor: pointer;
              list-style: none;
              font-size: 18px;
              transition: 0.3s ease-in-out;
              &:hover{
                color: hsl(234, 39%, 85%);
              }
              &.active{
                color: hsl(220, 98%, 61%);
              }
            }
          }
    }
    }


  }
</style>
