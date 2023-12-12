

<template>
  <div>
    <Datepicker v-if="((new Date()).getTime() > (new Date(item.data.time)).getTime())" v-model="item.data.time" format="yyyy/MM/dd HH:mm" auto-apply dark></Datepicker>
    <Datepicker v-else-if="((new Date()).getTime() < (new Date(item.data.time)).getTime())" v-model="item.data.time" format="yyyy/MM/dd HH:mm" auto-apply></Datepicker>
    <input type="checkbox" v-model="item.data.done" v-on:click="emit('putdata', item.data.id, item.data.time, item.data.memo, !item.data.done)"/>
    <textarea v-model="item.data.memo"></textarea>
    <button v-on:click="emit('putdata', item.data.id, item.data.time, item.data.memo, item.data.done)">編集</button>
    <button v-on:click="emit('deletedata', item.data.id)">削除</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Datepicker from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'

const item = defineProps({
  data: Object
})

const emit = defineEmits(['putdata', 'deletedata'])

console.log(item.data)

</script>
