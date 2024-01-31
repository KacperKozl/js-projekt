<template>
  <div class="grid-item">
    <p>{{ channel.name }}</p>
    <div>
    <MessageSingle v-model:userLogin="userLogin" v-for="message in messageList" :key="message.id" :message="message" @delete="deleteMessage" @edit="editMessage"/>
    </div>
    <form @submit="sendMessage">
      <textarea v-model="msg" required placeholder="New message" rows="4" ></textarea>
      <button class="addButton">Send</button>
    </form>
  </div>
  </template>
  
  <script setup>
  import MessageSingle from './MessageSingle.vue';
  import { ref } from 'vue'
  import { defineEmits } from 'vue';
  import { defineModel } from 'vue'
  const messageList=defineModel('messageList')
  const channel=defineModel('channel')
  const userLogin=defineModel('userLogin')
  const msg=ref();
  const emit=defineEmits(['synchroMessage']);
  async function sendMessage(e){
    e.preventDefault()
  const message={channelId:channel.value.id,text:msg.value,date:new Date().toDateString(),sender:userLogin.value}
  msg.value=null
  try{
  await fetch("http://localhost:8000/api/"+channel.value.type+"/sendMessage", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(message)
    }).then(()=>{
      emit("synchroMessage")
    })
  }catch(error){
    console.log(error)
  }
}
async function deleteMessage(messageId){
  console.log("messageId:" +messageId)
  const message={messageId:messageId}
  try{
  await fetch("http://localhost:8000/api/"+channel.value.type+"/deleteMessage", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(message)
    }).then(()=>emit("synchroMessage"))
  }catch(error){
    console.log(error)
  }
}
async function editMessage(messageToEdit){
  console.log(messageToEdit.text)
  try{
  await fetch("http://localhost:8000/api/"+channel.value.type+"/editMessage", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(messageToEdit)
    }).then(()=>emit("synchroMessage"))
  }catch(error){
    console.log(error)
  }
}
  </script>
  
  <style scoped>
  .grid-item{
    grid-column:2;
    grid-row: 1;
  }
  .addButton{
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
  width: 10%;
  vertical-align: middle;
}
textarea{
  font-size: 15px;
  margin-top:5px;
  resize: none;
  columns: auto;
  width:80%;
  vertical-align: middle;
}
p{
  font-size: 30px;
    font-weight: bold;
    font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
    margin: 10px;
}
  </style>
  