<template>
  <div>
    <form v-if="curentForm==='Login'" @submit="login">
      <label>Login in</label><br>
      <input v-model="user.login" required placeholder="Login">
      <br>
      <input v-model="user.password" type="password" required placeholder="Password">
      <br>
      <p v-if="loginError" class="error">Incorrect login or password</p>
      <button>Log in</button>
    </form>
    <form v-else @submit="register">
      <label>Registration</label><br>
        <input v-model="user.login" required placeholder="Login"><br>
        <input v-model="user.password" type="password" required placeholder="Password"><br>
        <input v-model="user.email" type="email" required placeholder="Email"><br>
        <p v-if="registerError" class="error">This login was already taken</p>
        <button>Register</button>
    </form>
    <button @click="changeForm">{{changeFormButtonText}} page</button>
  </div>
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
    const userSend={login:user.value.login,password:user.value.password}
    try{
    await fetch("http://localhost:8000/api/login", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(userSend)
    }).then(response=>{
      if(!response.ok) loginError.value=true;else
      return response.json()
    }).then(data=>{
      emit("login",{id:data.id,login:data.login});
    })
  }catch(error){
    console.log(error)
  }
  }
async function register(e){
    e.preventDefault();
    try{
    await fetch("http://localhost:8000/api/register", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(user.value)
    }).then(response=>{
      if(!response.ok) registerError.value=true;else
      changeForm();
    }
    )
  }catch(error){
    console.log(error)
  }
  }
  const changeForm=()=>{
    user.value={
        login:"",
      password:"",
      email:""}
    if(curentForm.value==='Login'){curentForm.value='Register';changeFormButtonText.value='Login'}
    else {curentForm.value='Login';changeFormButtonText.value='Register'}
  }
  </script>
  <style scoped>
  div{
    background-color: beige;
    margin:auto;
    width: fit-content;
    direction: center;
    align-content:center;
  }
  label{
    font-size: 50px;
    font-weight: bold;
    font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
    margin: 25px;
  }
  input{
    font-size: 30px;
    margin: 20px;
  }

button {
  background-color: orange;
  border: 1px solid transparent;
  border-radius: 3px;
  box-shadow: rgba(255, 255, 255, .4) 0 1px 0 0 inset;
  box-sizing: border-box;
  color: #fff;
  cursor: pointer;
  display: inline-block;
  font-family: -apple-system,system-ui,"Segoe UI","Liberation Sans",sans-serif;
  font-size: 20px;
  font-weight: 400;
  line-height: 1.15385;
  margin: 0;
  outline: none;
  padding: 8px .8em;
  position: relative;
  text-align: center;
  text-decoration: none;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  vertical-align: baseline;
  white-space: nowrap;
  margin: 10px;
}
.error{
  margin:auto;
  width: fit-content;
  background-color: #ffcccc;
  color: #b30000;
  font-size: 20px;
  font-weight: bold;
}
  </style>
  