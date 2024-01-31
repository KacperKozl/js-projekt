<template>
  <div>
    <p class="headerMessage"><b>{{ message.sender }}</b> <i>{{ message.date }}</i></p>
    <p class="textMessage">{{ message.text }}</p>

    <button class="deleteButton" v-if="userLogin===message.sender" @click="emit('delete',message.id)">Delete</button>
    <button class="editButton" v-if="userLogin===message.sender" @click="isDialogVisible=true">Edit</button>
  </div>
  <div class="modal" v-if="isDialogVisible===true">
    <div class="modal-content">
            Message: <textarea v-model="editText" required rows="4" ></textarea>
            <button @click="isDialogVisible=false">Cancel</button>
            <button class="editButton" @click="{isDialogVisible=false;emit('edit',{messageId:message.id,text:editText});}">Edit</button>
          </div>
    </div>
  </template>
  
  <script setup>
  import { defineModel } from 'vue'
  import { ref } from 'vue'
  import { defineEmits } from 'vue';
  import { defineProps } from 'vue';
  
  const props=defineProps(['message'])
  const editText=ref(props.message.text)
  const userLogin=defineModel('userLogin')
  const emit=defineEmits(['delete','edit'])
  let isDialogVisible=ref(false)

  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
 div {
    border: 3px black solid;
    background-color: white;
    border-radius: 25px;
    margin: 10px;
  }
  .editButton{
  background-color: green;
  border: 1px solid transparent;
  border-radius: 3px;
  box-shadow: rgba(255, 255, 255, .4) 0 1px 0 0 inset;
  box-sizing: border-box;
  color: #fff;
  cursor: pointer;
  font-family: -apple-system,system-ui,"Segoe UI","Liberation Sans",sans-serif;
  font-size: 15px;
  font-weight: 400;
  line-height: 1.15385;
  outline: none;
  padding: 4px ;
  position: relative;
  text-align: center;
  text-decoration: none;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  white-space: nowrap;
  margin: 6px;
}
.deleteButton {
  background-color: red;
  border: 1px solid transparent;
  border-radius: 3px;
  box-shadow: rgba(255, 255, 255, .4) 0 1px 0 0 inset;
  box-sizing: border-box;
  color: #fff;
  cursor: pointer;
  font-family: -apple-system,system-ui,"Segoe UI","Liberation Sans",sans-serif;
  font-size: 15px;
  font-weight: 400;
  line-height: 1.15385;
  outline: none;
  padding: 4px ;
  position: relative;
  text-align: center;
  text-decoration: none;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  white-space: nowrap;
  margin: 6px;
}
.headerMessage{
  text-align: left;
  margin-left: 10px;
  margin-bottom: 5px;
}
.textMessage{
  text-align: left;
  margin-left: 10px;
  margin-top: 5px;
}
.modal{
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%; /* Full height */
  background-color: rgb(0,0,0); /* Fallback color */
  background-color: rgba(0,0,0,0.4); /* Black w/ opacity */
}
.modal-content{
  background-color: #fefefe;
  margin: 15% auto; /* 15% from the top and centered */
  padding: 20px;
  border: 1px solid #888;
  width: 80%; /* Could be more or less, depending on screen size */
}
  </style>
  