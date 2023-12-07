<child-component @eventEmit="updateEvent" />
<template>
    <Datepicker v-model="date" format="yyyy/MM/dd HH:mm" placeholder="日付を選択してください" auto-apply></Datepicker>
    <div><textarea v-model="message" placeholder="文字を入力してください"></textarea></div>
      <button v-on:click="postdata">作成</button>
      <button v-on:click="view">更新</button>
    <div>並び替え
      <select v-model="selected">
        <option disabled value=""></option>
        <option>旧</option>
        <option>新規</option>
        <option>日付</option>
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
import List from './list.vue'

let {data} = reactive(await useFetch('http://localhost:3200/posts'))
const date = ref(new Date())
const message = ref()
const menu = ref(true)

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

async function deletedata(i) {
  try {
    await fetch('http://localhost:3200/posts/'+String(i), {
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
    data = await response.data
    console.log("Success:", data)
    message.value = ''
  } catch (error) {
    console.error("Error:", error)
  }
}



</script>