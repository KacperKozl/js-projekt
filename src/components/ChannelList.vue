<template>
  <div class="grid-item">
    <form @submit="subscribeToChannel">
      <input v-model="newChannelName" required placeholder="Add new channel">
      <button>Add</button>
    </form>
    <div v-for="channel in channelList" :key="channel.id" @click="changeCurrentChannel(channel)">
      {{ channel.name }}
      <button @click="deleteChannel(channel)">Delete</button>
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
    emit('changeCurrentChannel',channel)
  }
  function deleteChannel(channel){
    emit('deleteChannel',channel.id)
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
  </style>
  