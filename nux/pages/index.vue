<child-component @eventEmit="updateEvent" />
<template>
    <Datepicker v-model="date" format="yyyy/MM/dd HH:mm" placeholder="日付を選択してください" auto-apply></Datepicker>
    <div><textarea v-model="message" placeholder="文字を入力してください"></textarea></div>
      <button v-on:click="postdata">作成</button>
      <button v-on:click="getdata">更新</button>
    <div>並び替え
      <select v-model="selected">
        <option disabled></option>
        <option value="0">旧追加</option>
        <option value="1">新規追加</option>
        <option value="2">旧日付</option>
        <option value="3">新規日付</option>
      </select>
    </div>
    <div>
      {{ donelist }}
      <select v-model="donelist">リスト
        <option value=0>未完了</option>
        <option value=1>完了</option>
      </select>
    </div>
    <div v-for='item in data' :key="item.id">
      <div>
        <Datepicker v-if="((new Date()).getTime() > (new Date(item.time)).getTime())" v-model="item.time" format="yyyy/MM/dd HH:mm" auto-apply dark></Datepicker>
        <Datepicker v-else-if="((new Date()).getTime() < (new Date(item.time)).getTime())" v-model="item.time" format="yyyy/MM/dd HH:mm" auto-apply></Datepicker>
        <input v-if="donelist" type="checkbox" v-model="item.done" v-on:click="putdata(item.id, item.time, item.memo, !item.done)"/>
        <input v-else-if="!donelist" type="checkbox" v-model="item.done" v-on:click="putdata(item.id, item.time, item.memo, !item.done)"/>
        <textarea v-if="donelist" v-model="item.memo"></textarea>
        <textarea v-else-if="!donelist" v-model="item.memo"></textarea>
        <button v-on:click="putdata(item.id, item.time, item.memo, item.done)">編集</button>
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
const donelist = ref()
let color = ref("#f06060")

async function postdata() {
  try {
    await fetch("http://localhost:3200/posts", {
      method: "POST",
      headers: { "Content-Type": "application/json"},
      body: JSON.stringify({ time: date.value, memo: message.value, done: false})
    });
    await getdata();
  } catch (error) {
    console.error("Error:", error);
  }
}

async function putdata(id, time, memo, done){
  try{
    await console.log(done)
    await fetch('http://localhost:3200/posts/'+String(id), {
      method: "PUT",
      headers: { "Content-Type": "application/json"},
      body: JSON.stringify({ time: time, memo: memo, done: done})
    });
    await getdata();
  }catch(error){
    console.error("Error:", error)
  }
}

async function deletedata(id) {
  try {
    await fetch('http://localhost:3200/posts/'+String(id), {
        method: 'DELETE'
    })
    await getdata();
  }catch (error) {
    console.error("Error:", error)
  }
}

async function getdata() {
  try {
    const response = await useFetch("http://localhost:3200/posts")
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
    console.log("2",data.value.sort((a, b) => (new Date(a.time)).getTime() - (new Date(b.time)).getTime()))
  }
  else if(i == 3){
    console.log("2",data.value.sort((a, b) => (new Date(b.time)).getTime() - (new Date(a.time)).getTime()))
  }
})

watch(donelist, (i) => {
  console.log(i)
  i === "0" ? color.value = "#f06060" : color.value = "#ffffff"
  console.log(donelist.value)
})

</script>

<style>
.dp__theme_dark {
    --dp-background-color: v-bind(color);
    --dp-text-color: #000000;
    --dp-hover-color: #484848;
    --dp-hover-text-color: #fff;
    --dp-hover-icon-color: #959595;
    --dp-primary-color: red;
    --dp-primary-disabled-color: #61a8ea;
    --dp-primary-text-color: #fff;
    --dp-secondary-color: yellow;
    --dp-border-color: #2d2d2d;
    --dp-menu-border-color: red;
    --dp-border-color-hover: #aaaeb7;
    --dp-disabled-color: #737373;
    --dp-disabled-color-text: #d0d0d0;
    --dp-scroll-bar-background: #212121;
    --dp-scroll-bar-color: #484848;
    --dp-success-color: blue;
    --dp-success-color-disabled: #428f59;
    --dp-icon-color: #959595;
    --dp-danger-color: #e53935;
    --dp-marker-color: #e53935;
    --dp-tooltip-color: #3e3e3e;
}

input[type=checkbox] {
  transform: scale(1.5);
}
</style>
