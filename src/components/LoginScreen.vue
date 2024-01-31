<template>
    <form v-if="curentForm==='Login'" @submit="login">
      <input v-model="user.login" required placeholder="Login">
      <input v-model="user.password" type="password" required placeholder="Password">
      <p v-if="loginError">Niepoprawne dane logowania</p>
      <button>Log in</button>
    </form>
    <form v-else @submit="register">
        <input v-model="user.login" required placeholder="Login">
        <input v-model="user.password" type="password" required placeholder="Password">
        <input v-model="user.email" required placeholder="Email">
        <p v-if="loginError">Użytkownik z takim loginem już istnieje</p>
        <button>Register</button>
    </form>
    <button @click="changeForm">{{changeFormButtonText}}</button>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import { defineEmits } from 'vue';
  let changeFormButtonText=ref("Register")
  let curentForm=ref('Login')
  let loginError=ref()
  let registerError=ref()
  const user=ref({
    login:"",
    password:"",
    email:"",
  })
  const emit=defineEmits(['login']);
  async function login(e){
    e.preventDefault();
    await fetch("http://localhost:8000/api/login", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({login:user.value.login,password:user.value.password})
    }).then(data=>{
      emit("login",{id:data.id,login:data.login});
    }).catch(
      loginError.value=true
    )
  }
async function register(e){
    e.preventDefault();
    await fetch("http://localhost:8000/api/register", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(user.value)
    }).then(()=>{
      user.value={
        login:"",
      password:"",
      email:""}
      changeForm();
    }
    ).catch(
      registerError.value=true
    )
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
  