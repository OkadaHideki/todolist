

<template>
  <div>
    <v-row>
      <v-col cols="2" class="d-flex flex-row align-center">
        <v-checkbox color="#EBAB52" class="d-flex flex-row justify-center align-center" v-model="item.data.done" v-on:click="emit('putdata', item.data.id, item.data.time, item.data.memo, !item.data.done)"/>
      </v-col>
      <v-col cols="8">
        <Datepicker v-if="((new Date()).getTime() > (new Date(item.data.time)).getTime())" v-model="item.data.time" format="yyyy/MM/dd HH:mm" auto-apply dark></Datepicker>
        <Datepicker v-else-if="((new Date()).getTime() < (new Date(item.data.time)).getTime())" v-model="item.data.time" format="yyyy/MM/dd HH:mm" auto-apply></Datepicker>
        <v-textarea v-model="item.data.memo"></v-textarea>
      </v-col>
      <v-col cols="2" class="d-flex flex-row align-center">
        <v-btn color="#9DEB52" v-on:click="emit('putdata', item.data.id, item.data.time, item.data.memo, item.data.done)">編集</v-btn>
        <v-btn color="#52EBE5" v-on:click="emit('deletedata', item.data.id)">削除</v-btn>
      </v-col>
    </v-row>
  </div>
</template>

<script setup>
import Datepicker from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'

const item = defineProps({
  data: Object
})

const emit = defineEmits(['putdata', 'deletedata'])

console.log(item.data)

</script>
