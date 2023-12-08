<child-component @eventEmit="updateEvent" />
<template>
    <Datepicker v-model="date" format="yyyy/MM/dd HH:mm" placeholder="日付を選択してください" auto-apply></Datepicker>
    <div><textarea v-model="message" placeholder="文字を入力してください"></textarea></div>
      <button v-on:click="postdata">作成</button>
      <button v-on:click="view">更新</button>
    <div>並び替え
      <select v-model="selected">
        <option disabled></option>
        <option value="0">旧追加</option>
        <option value="1">新規追加</option>
        <option value="2">旧日付</option>
      </select>
    </div>
    <div v-for='item in data' :key="item.id">
      <div>
        <Datepicker v-model="item.time" format="yyyy/MM/dd HH:mm" auto-apply></Datepicker>
        <textarea v-model="item.memo"></textarea>
        <button v-on:click="putdata(item.id, item.time, item.memo)">編集</button>
        <button v-on:click="deletedata(item.id)">削除</button>
      </div>
    </div>
</template>

<script setup>
import {ref, watch} from 'vue'
import Datepicker from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'

let data = ref(await useFetch('http://localhost:3200/posts').data)
const date = ref(new Date())
const message = ref()
const selected = ref()

async function postdata() {
  try {
    await fetch("http://localhost:3200/posts", {
      method: "POST",
      headers: { "Content-Type": "application/json"},
      body: JSON.stringify({ time: date.value, memo: message.value})
    });
    await view();
  } catch (error) {
    console.error("Error:", error);
  }
}

async function putdata(id, time, memo){
  try{
    await fetch('http://localhost:3200/posts/'+String(id), {
      method: "PUT",
      headers: { "Content-Type": "application/json"},
      body: JSON.stringify({ time: time, memo: memo})
    });
    await view();
  }catch(error){
    console.error("Error:", error)
  }
}

async function deletedata(id) {
  try {
    await fetch('http://localhost:3200/posts/'+String(id), {
        method: 'DELETE'
    })
    await view();
  }catch (error) {
    console.error("Error:", error)
  }
}

async function view() {
  try {
    const response = await useFetch("http://localhost:3200/posts")
    await data.value.splice(0)
    data.value = response.data.value
    console.log("Success:", data)
  } catch (error) {
    console.error("Error:", error)
  }
}

watch(selected, (i) => {
  if(i == 0){
    console.log("0",data.value.sort((a, b) => a.id - b.id))
  }
  else if(i == 1){
    console.log("1",data.value.sort((a, b) => b.id - a.id))
  }
  else if(i == 2){
    console.log("2",data.value.sort((a, b) => a.time.getTime() > b.time.getTime()))
  }
})

console.log(data.value)

</script>