<child-component @eventEmit="updateEvent" />
<template>
    <Datepicker format="yyyy/MM/dd/HH/mm" model-type="yyyy-MM-dd-HH-mm" v-model="date" placeholder="日付を選択してください"></Datepicker>
    <div><textarea v-model="message" placeholder="文字を入力してください"></textarea></div>
    <button v-on:click="add">作成</button>
    <button v-on:click="view">更新</button>
    <div v-for='item in data' :key="item.id">
      <div>{{ item.time }} {{ item.memo }}<button v-on:click="deletedata(item.id)">削除</button></div>
    </div>
</template>

<script setup>
import {ref, watch} from 'vue'
import Datepicker from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'

const date = ref(new Date())
const message = ref()
let {data} = reactive(await useFetch('http://localhost:3200/posts'))

async function add() {
  try {
    const response = await
    fetch("http://localhost:3200/posts", {
      method: "POST",
      headers: { "Content-Type": "application/json"},
      body: JSON.stringify({ time: date.value, memo: message.value})
    });
    const result = await response.json();
    await view();
  } catch (error) {
    console.error("Error:", error);
  }
}

async function view() {
  try {
    const response = await useFetch("http://localhost:3200/posts")
    const result = await response
    data = await result.data
    console.log("Success:", data)
    message.value = ''
  } catch (error) {
    console.error("Error:", error)
  }
}

async function deletedata(i) {
  try {
    const response = await
    fetch('http://localhost:3200/posts/'+String(i), {
        method: 'DELETE'
    })
    await view();
  }catch (error) {
    console.error("Error:", error)
  }
}

watch(data)

</script>