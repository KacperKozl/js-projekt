<template>
  <div class="grid-item">
    <p>{{ channel.name }}</p>
    <MessageSingle v-model:userLogin="userLogin" v-for="message in messageList" :key="message.id" :message="message" @delete="deleteMessage" @edit="editMessage"/>
    <form @submit="sendMessage">
      <input v-model="msg" required placeholder="New message">
      <button>Send</button>
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
  //const channelType=defineModel('channelType')
  const msg=ref();
  const emit=defineEmits(['synchroMessage']);
  async function sendMessage(){
  const message={channelId:channel.value.id,text:msg.value,date:new Date().toDateString(),sender:userLogin.value}
  try{
  await fetch("http://localhost:8000/api/"+channel.value.type+"/sendMessage", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(message)
    }).then(emit("synchroMessage"))
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
    }).then(emit("synchroMessage"))
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
    }).then(emit("synchroMessage"))
  }catch(error){
    console.log(error)
  }
}
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  h3 {
    margin: 40px 0 0;
  }
  ul {
    list-style-type: none;
    padding: 0;
  }
  li {
    display: inline-block;
    margin: 0 10px;
  }
  a {
    color: #42b983;
  }
  .grid-item{
    overflow: scroll;
    grid-column:2;
    grid-row: 1;
  }
  </style>
  