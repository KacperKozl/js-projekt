<template>
  <div>
    <p>{{ message.sender }} {{ message.date }}</p>
    <p>{{ message.text }}</p>

    <button v-if="userLogin===message.sender" @click="emit('delete',message.id)">Usuń</button>
    <button v-if="userLogin===message.sender" @click="{visibleDialog=true;editText=message.text}">Edytuj</button>
  </div>
  <dialog v-bind:open="visibleDialog">
            Message:<input type="text" v-model="editText">
            <button @click="visibleDialog=false">Cancel</button>
            <button @click="{emit('edit',{messageId:message.id,text:editText});visibleDialog=false}">Edit</button>
    </dialog>
  </template>
  
  <script setup>
  import { defineModel } from 'vue'
  import { ref } from 'vue'
  import { defineEmits } from 'vue';
  const visibleDialog=ref()
  const editText=ref()
  const message=defineModel('message')
  const userLogin=defineModel('userLogin')
  const emit=defineEmits(['delete','edit'])
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
 div {
    border: 3px black solid;
  }
  </style>
  