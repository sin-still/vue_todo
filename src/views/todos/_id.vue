<template lang="">
   <div class = "wrap">
      
      <h1>To-Do Page</h1>
      <div v-if = "loading">
         loading...
      </div>
      <form v-else @submit.prevent="onSave">
         <div class = "row">
            <div class ="form-group">
               <label>-todo Subject-</label>
               <input type="text" v-model="todo.subject">
            </div>
            <div class="form-group">
               <label class="display">Status</label>
               <div class ="position">
                  <button @click="toggleTodoStatus" class="btn btn100" type="button" :class="todo.completed ? 'btnG' : 'btnR'">{{todo.completed ? 'COMPLETE!' : "Ing......."}}</button>
               </div>
            </div>
         </div>
         <div class ="center">
            <button @click="onSave" class="btn btnS" :class="[todoUpdated ? 'btnR' : '']" type="submit" :disabled="!todoUpdated">저장</button>
            <button class="btn btnE" @click="moveToTodoListPage">취소</button>
         </div>
      </form>
   </div>
</template>
<script>
import { useRoute, useRouter } from 'vue-router';
import axios from 'axios';
import { ref, computed } from 'vue';
import _ from 'lodash'


export default {
   setup(){
      const route = useRoute();
      const router = useRouter();
      const todo = ref(null);
      const loading = ref(true);
      const todoId = route.params.id;
      const originalTodo = ref(null)      

      const getTodo = async () => {
         const res = await axios.get(`http://localhost:3000/todos/${todoId}`)
         todo.value = {...res.data};
         originalTodo.value={...res.data}
         loading.value = false;
         console.log(todo.value.subject)
      }
      getTodo()
      const toggleTodoStatus = () =>{
         todo.value.completed = !todo.value.completed;

      }
      const moveToTodoListPage = () =>{
         router.push({
            name: "Todos",

         })
      }
      const todoUpdated = computed(()=>{
         return !_.isEqual(todo.value, originalTodo.value)
      })
      const onSave = async ()=>{
         const res = await axios.put(`http://localhost:3000/todos/${todoId}`, {
            subject: todo.value.subject, 
            completed: todo.value.completed
         })
         originalTodo.value={...res.data}
      }
      return {
        todo,
        loading,
        toggleTodoStatus,
        moveToTodoListPage,
        onSave,
        todoUpdated,
        
      }
   }   
}
</script>
<style lang="scss">
   .wrap{
      width: 1000px;
      margin: 0 auto;
      text-align: left;
      >h1{
         text-align: center;
         
         color: #f0a5e2;
      }
      >div{}
      >form{
         .form-group{
            display: flex;
            flex-direction: column;
            position: relative;;
            label{
               font-size: 15px;
               display: block;
               margin-bottom: 10px;
               font-weight: bold;
            }
            input{
               padding: 5px 10px;
               border: 1px solid #ddd;
               box-sizing: border-box;
            }
            .display{display: none;}
            .position{
               width: 100%;
            }
         }
         .btn{border: none;padding: 5px 3px; border-radius: 3px;margin: 5px 3px; font-weight: bold;}
         .btnR{background: rgb(255, 0, 0);}
         .btnG{background: green;}
         .btn100{width: 100%; margin-top: 30px; padding: 10px 0; color: #fff;}
         .btnS{width: 10%; color: #fff;}
         .btnE{width: 10%;}
         .center{width: 100%; text-align: center;margin-top: 30px;}
      }
   }
</style>