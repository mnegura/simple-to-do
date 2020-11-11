<template lang='pug'>
    #app.todo-app
      to-do
</template>

<script lang='ts'>
import { defineComponent } from '@vue/composition-api';
import Vue from 'vue';

import Todo from './components/Todo.vue';

Vue.component('to-do', Todo);

export default defineComponent({
  name: 'App',
});
</script>

<style lang='scss'>
@import '@/styles/index.scss';
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@100;300;500&display=swap");
</style>
