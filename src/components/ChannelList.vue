<template>
  <div class="grid-item">
    <form @submit="subscribeToChannel">
      <input v-model="newChannelName" required placeholder="Add new channel">
      <button class="addButton">Add</button>
    </form>
    <div class="listItem" v-for="channel in channelList" :key="channel.id" @click="changeCurrentChannel(channel)" v-bind:class="{newMessage:isNewMessage(channel)}">
      <label>{{ channel.name }}</label>
      <button @click="deleteChannel(channel)" class="deleteButton">Delete</button>
    </div>
  </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import { defineEmits } from 'vue';
  import { defineModel } from 'vue'
  const channelList=defineModel('channelList')
  const newChannelName=ref();
  const emit=defineEmits(['addNew','changeCurrentChannel','deleteChannel']);
  const subscribeToChannel=(e)=>{
    e.preventDefault();
    emit("addNew",newChannelName.value);
  }
  function changeCurrentChannel(channel){
    channel.oldMsgId=channel.lastMessageId
    emit('changeCurrentChannel',channel)
  }
  function deleteChannel(channel){
    emit('deleteChannel',channel.id)
  }
  function isNewMessage(channel){
    return channel.lastMessageId>channel.oldMsgId
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
    grid-column:1;
    grid-row: 1;
  }
 
  .listItem{
    border-radius: 10px;
    margin:4px;
    background-color: lightblue;
    color: black;
    font-size: 20px;
    font-weight: 400;
    vertical-align: middle;
    position: relative;
  }
  .listItem::after{

    display: table;
    clear:both;
    content: "";
  }
  .newMessage{
    color: red;
    font-weight: bold;
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
  display: block;
  float:right;
}
label{
  position: absolute; top: 50%; transform: translateY(-50%); left:15px;
}
input{
  font-size: 15px;
  margin:5px;
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
}
  </style>
  