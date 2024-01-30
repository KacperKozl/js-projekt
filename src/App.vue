<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <LoginScreen v-if="userId<0" @login="setLogId"/>
  <div class="grid" v-else>
  <ChannelList v-if="listType==='channels'" v-model:channelList="channels" @addNew="subscribeToChannel" @changeCurrentChannel="changeCurrentChannel" @deleteChannel="unsubscribeChannel"/>
  <ChannelList v-if="listType==='private'" v-model:channelList="privates" @addNew="newPrivate" @changeCurrentChannel="changeCurrentChannel" @deleteChannel="unsubscribePrivate"/>
  <button @click="changeListType">Zmień</button>
  <MessageView v-if="currentChannel!=null" v-model:channel="currentChannel" v-model:messageList="messages" v-model:userLogin="userLogin" @synchroMessage="synchronizeMessages"/>
</div>
</template>

<script setup>
import {ref} from 'vue'
import LoginScreen from './components/LoginScreen.vue'
import ChannelList from './components/ChannelList.vue'
import MessageView from './components/MessageView.vue';
const userLogin=ref('none');
const userId=ref(1)
const currentChannel=ref()
const listType=ref("channels")
function setLogId(loginUserId){
  userId.value=loginUserId;
}
const channels=ref([
  {id:1,name:"test1",type:"channel"},
  {id:2,name:"test2",type:"channel"}
])
const privates=ref([
  {id:1,name:"priv1",type:"private"},
  {id:2,name:"priv2",type:"private"}
])
const messages=ref([
  {id:1,text:'raz',date:'now',sender:'none'},
  {id:2,text:'dwa',date:'now',sender:'someone'}
])
async function subscribeToChannel(channelName){
  const subscribedChannel={channelName:channelName,userLogin:userLogin.value}
  try{
  await fetch("http://localhost:8000/api/channel/subscribe", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(subscribedChannel)
    }).then(synchronizeData())
  }catch(error){
    console.log(error)
  }
}
async function unsubscribeChannel(channelId){
  const unsubscribedChannel={channelId:channelId,userLogin:userLogin.value}
  try{
  await fetch("http://localhost:8000/api/channel/unsubscribe", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(unsubscribedChannel)
    }).then(synchronizeData())
  }catch(error){
    console.log(error)
  }
}
async function newPrivate(privateName){
  const subscribedPrivate={privateName:privateName,userLogin:userLogin.value}
  try{
  await fetch("http://localhost:8000/api/private/subscribe", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(subscribedPrivate)
    }).then(synchronizeData())
  }catch(error){
    console.log(error)
  }
}
async function unsubscribePrivate(privateId){
  const unsubscribedPrivate={privateId:privateId}
  try{
  await fetch("http://localhost:8000/api/private/unsubscribe", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(unsubscribedPrivate)
    }).then(synchronizeData())
  }catch(error){
    console.log(error)
  }
}
async function changeCurrentChannel(channel){
  currentChannel.value=channel
  console.log(channel.id)
  synchronizeMessages()
}
function changeListType(){
  if(listType.value==='channels') listType.value='private';
  else listType.value='channels';
}
function synchronizeData(){
  synchronizeChannels()
  synchronizePrivates()
  synchronizeMessages()
}
async function synchronizeChannels(){
  try{
  await fetch("http://localhost:8000/api/channelAll", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(userLogin.value)
    }).then(response=>response.json).then(data=>channels.value=data.array.forEach(element => 
      element.type='channel'
    ))
  }catch(error){
    console.log(error)
  }
}
async function synchronizePrivates(){
  try{
  await fetch("http://localhost:8000/api/privateAll", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(userLogin.value)
    }).then(response=>response.json).then(data=>privates.value=data.array.forEach(element=>element.type='private'))
  }catch(error){
    console.log(error)
  }
}
async function synchronizeMessages(){
  if(currentChannel.value===null) return;
  try{
  await fetch("http://localhost:8000/api/"+currentChannel.value.type+"/messagesAll", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(currentChannel.value.id)
    }).then(response=>response.json).then(data=>messages.value=data)
  }catch(error){
    console.log(error)
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.grid{
  display: grid;
}
#ChannelList{
  grid-column-start: 1;
  grid-column-end: 1;
}
#MessageList{
  grid-column-start: 2;
  grid-column-end: 2;
}
</style>
