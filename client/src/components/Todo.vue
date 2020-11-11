<template lang="pug">
  .main-container
    div.error-msg(v-show="error.length")
      span {{ error }}
      .button.button--state-remove(@click="clearError")

    .search
      div.search__btn
      input.search__input(
        placeholder='Search'
        v-bind:value="search"
        @input="updateSearchValue"
      )

    .todo
      .todo__form
        input.todo__input(placeholder='Take a note' v-model="description" @keyup.enter="addTask")
        button.button.button--state-add(@click="addTask")
      span.loading-txt(v-if="loadingGet") Loading...
      .todo__results(v-if="!loadingGet")
        task(
          v-for="task in filteredTodos"
          :key="task._id"
          :task="task"
          @delete-task="deleteTask"
          @update-task="updateTask"
        )
</template>

<script lang="ts">
import {
  defineComponent, onMounted, ref, computed,
} from '@vue/composition-api';
import Vue from 'vue';

// eslint-disable-next-line import/no-unresolved,import/extensions
import { ITask } from '../models/task';
import Task from './Task.vue';

Vue.component('task', Task);

export default defineComponent({
  name: 'Todo',
  setup() {
    const error = ref('');
    const search = ref('');
    const description = ref('');
    const loadingGet = ref(false);
    const todos = ref<ITask[]>([]);

    const filteredTodos = computed(() => todos.value
      .filter((task) => task.description.toLowerCase().includes(search.value.toLowerCase())));

    const updateSearchValue = (event: { target: { value: string }}) => {
      setTimeout(() => { search.value = event.target.value; }, 500);
    };

    const getTodos = async () => {
      loadingGet.value = true;
      await fetch('http://localhost:3000/api/v1/todo')
        .then((resp) => resp.json())
        .then((data: ITask[]) => { todos.value = data.reverse(); })
        .catch((errorMsg: string) => { error.value = errorMsg; })
        .finally(() => {
          loadingGet.value = false;
        });
    };

    const deleteTask = (id: string) => {
      fetch(`http://localhost:3000/api/v1/todo/${id}`, {
        method: 'DELETE',
      }).then(() => {
        todos.value = todos.value.filter(
          (t: ITask) => t._id !== id,
        );
      }).catch((errorMsg: string) => {
        error.value = errorMsg;
      });
    };

    const updateTask = (model: {id: string, done: boolean}) => {
      fetch(`http://localhost:3000/api/v1/todo/${model.id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          done: model.done,
        }),
      }).then(() => {
        const elIndex = todos.value.findIndex((item) => item._id === model.id);

        if (elIndex !== -1) todos.value[elIndex].done = model.done;
      }).catch((errorMsg: string) => {
        error.value = errorMsg;
      });
    };

    const addTask = () => {
      if (description.value.trim().length) {
        fetch('http://localhost:3000/api/v1/todo/', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            description: description.value.trim(),
          }),
        }).then((resp) => resp.json())
          .then((data: ITask) => {
            todos.value.unshift(data);
            description.value = '';
          }).catch((errorMsg: string) => {
            error.value = errorMsg;
          });
      }
    };

    const clearError = () => {
      error.value = '';
    };

    onMounted(getTodos);

    return {
      error,
      description,
      todos,
      search,
      loadingGet,
      deleteTask,
      updateTask,
      addTask,
      clearError,
      filteredTodos,
      updateSearchValue,
    };
  },
});
</script>
