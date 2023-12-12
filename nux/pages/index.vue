<child-component @eventEmit="updateEvent" />
<template>
  <v-app>
    <v-app-bar color="#52EB7D">
      <v-app-bar-title>
        Todoリスト
      </v-app-bar-title>
    </v-app-bar>

    <v-main>
    <v-row>
      <v-col cols="1"></v-col>
      <v-col cols="9">
        <Datepicker v-model="date" format="yyyy/MM/dd HH:mm" placeholder="日付を選択してください" auto-apply></Datepicker>
        <v-textarea v-model="message"></v-textarea>
      </v-col>
      <v-col cols="2" class="d-flex flex-row justify-center align-center">
        <v-btn color="#5DEB52" v-on:click="postdata">作成</v-btn>
        <v-btn color="#52EBB1" v-on:click="getdata">更新</v-btn>
      </v-col>
    </v-row>
    <v-row>
      <v-col><v-select v-model="selected" item-title="title" item-value="value" :items="sortmode"></v-select></v-col>
      <v-col><v-select v-model="donelist" item-title="title" item-value="value" :items="checklist"></v-select></v-col>
    </v-row>
    <div v-for='item in data' :key="item.id">
      <div>
        <ListComp v-if="donelist==item.done" :data="item" @putdata="putdata" @deletedata="deletedata"/>
      </div>
    </div>
  </v-main>
  </v-app>
</template>

<script setup>
import {ref, watch} from 'vue'
import Datepicker from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'
import ListComp from './list.vue'

let data = ref(await useFetch('http://localhost:3200/posts').data)
const date = ref(new Date())
const message = ref()
const selected = ref('0')
const donelist = ref('0')
let color = ref("#9752EB")
const checklist = [
  { title: '未完了', value: '0' },
  { title: '完了済', value: '1' },
];
const sortmode = [
  { title: '旧追加', value: '0' },
  { title: '新規追加', value: '1' },
  { title: '旧日付', value: '2' },
  { title: '新規日付', value: '3' },
];

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
    data.value.sort((a, b) => a.id - b.id)
  }
  else if(i == 1){
    data.value.sort((a, b) => b.id - a.id)
  }
  else if(i == 2){
    data.value.sort((a, b) => (new Date(a.time)).getTime() - (new Date(b.time)).getTime())
  }
  else if(i == 3){
    data.value.sort((a, b) => (new Date(b.time)).getTime() - (new Date(a.time)).getTime())
  }
})

watch(donelist, (i) => {
  i === "0" ? color.value = "#9752EB" : color.value = "#ffffff"
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
