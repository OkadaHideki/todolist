<child-component @eventEmit="updateEvent" />
<template>
    <Datepicker v-model="date"></Datepicker>
    <div><textarea v-model="message"></textarea></div>
    <button>作成</button>
    <ChildComp :deadline="date" :message="message" />
    
</template>

<script setup>
import {ref} from 'vue'
import ChildComp from './list.vue'
import Datepicker from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'

const date = ref(new Date())
const message = ref('')


</script>