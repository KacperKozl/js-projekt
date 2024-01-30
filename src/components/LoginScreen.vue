<template>
    <form v-if="curentForm==='Login'" @submit="login">
      <input v-model="user.login" required placeholder="Login">
      <input v-model="user.password" type="password" required placeholder="Password">
      <button>Log in</button>
    </form>
    <form v-else @submit="register">
        <input v-model="user.login" required placeholder="Login">
        <input v-model="user.password" type="password" required placeholder="Password">
        <input v-model="user.email" required placeholder="Email">
        <button>Register</button>
    </form>
    <button @click="changeForm">{{changeFormButtonText}}</button>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import { defineEmits } from 'vue';
  let changeFormButtonText=ref("Register")
  let curentForm=ref('Login')
  const user=ref({
    login:"",
    password:"",
    email:"",
    userid:1,
  })
  const emit=defineEmits(['login']);
  const login=(e)=>{
    e.preventDefault();
    emit("login",user.value.userid);
  }
  const register=(e)=>{
    e.preventDefault();
    changeForm()
  }
  const changeForm=()=>{
    if(curentForm.value==='Login'){curentForm.value='Register';changeFormButtonText.value='Login'}
    else {curentForm.value='Login';changeFormButtonText.value='Register'}
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
  </style>
  