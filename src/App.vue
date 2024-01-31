<!-- eslint-disable no-unused-vars -->
<template>
  <img alt="App logo" src="./assets/logo.png">
  <LoginScreen v-if="!userId" @login="setLog"/>
  <div class="grid" v-else>
    <ChannelList v-if="listType==='channels'" v-model:channelList="channels" @addNew="subscribeToChannel" @changeCurrentChannel="changeCurrentChannel" @deleteChannel="unsubscribeChannel"/>
    <ChannelList v-if="listType==='private'" v-model:channelList="privates" @addNew="newPrivate" @changeCurrentChannel="changeCurrentChannel" @deleteChannel="unsubscribePrivate"/>
    <button @click="changeListType" class="listButton">{{listType.toLocaleUpperCase()}}</button>
    <MessageView v-if="currentChannel!=null" v-model:channel="currentChannel" v-model:messageList="messages" v-model:userLogin="userLogin" @synchroMessage="synchronizeMessages"/>
  </div>
</template>

<script setup>
import {ref} from 'vue'
import LoginScreen from './components/LoginScreen.vue'
import ChannelList from './components/ChannelList.vue'
import MessageView from './components/MessageView.vue';
const userLogin=ref('none');
const userId=ref()
const currentChannel=ref()
const listType=ref("channels")
function setLog(loginUser){
  userId.value=loginUser.id;
  userLogin.value=loginUser.login;
  //setInterval
  synchronizeData()
}
const channels=ref([
])
const privates=ref([
])
const messages=ref([
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
      body: JSON.stringify({userLogin:userLogin.value})
    }).then(response=>response.json()).then(data=>{
      data.forEach(newChan=>{
        newChan.oldMsgId=channels.value.find(old=>newChan.id-1===old.id)?.id??newChan.lastMessageId
        newChan.type='channel'
      })
      channels.value=data})
  }catch(error){
    console.log(error)
  }
}
async function synchronizePrivates(){
  try{
  await fetch("http://localhost:8000/api/privateAll", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({userLogin:userLogin.value})
    }).then(response=>response.json()).then(data=>{
      console.log(data)
      data.forEach(newChan=>{
        newChan.oldMsgId=privates.value.find(old=>newChan.id===old.id)?.id??newChan.lastMessageId
        newChan.type='private'
      })
      privates.value=data
    })
  }catch(error){
    console.log(error)
  }
}
async function synchronizeMessages(){
  if(!currentChannel.value) return;
  try{
  await fetch("http://localhost:8000/api/"+currentChannel.value.type+"/messagesAll", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({channelId:currentChannel.value.id})
    }).then(response=>response.json()).then(data=>{
      messages.value=data
    })
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
  background-color: #ffffcc;
  padding: 20px;
}
.grid{
  display: grid;
  background-color: beige;
  margin:auto;
  padding-bottom: 20px;
  width: fit-content;
  grid-template-columns:1fr 2fr;
}
#ChannelList{
  grid-column-start: 1;
  grid-column-end: 1;
}
#MessageList{
  grid-column-start: 2;
  grid-column-end: 2;
}
.listButton{
  width: fit-content;
  margin: auto;
  background-color: orange;
  border: 1px solid transparent;
  border-radius: 3px;
  box-shadow: rgba(255, 255, 255, .4) 0 1px 0 0 inset;
  box-sizing: border-box;
  color: #fff;
  cursor: pointer;
  display: inline-block;
  font-family: -apple-system,system-ui,"Segoe UI","Liberation Sans",sans-serif;
  font-size: 15px;
  font-weight: bold;
  line-height: 1.15385;
  outline: none;
  padding: 8px;
  position: relative;
  text-align: center;
  text-decoration: none;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  vertical-align: baseline;
  white-space: nowrap;
  grid-row: 2;
  grid-column:1;
}
</style>
