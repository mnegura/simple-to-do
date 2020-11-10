<template lang='pug'>
  .todo__result
    .todo__result-task-row
      label.checkbox__wrapper.todo__checkbox(@click='updateTask')
        input.checkbox--hidden.checkbox--primary(type='checkbox' :checked='task.done')
        .checkbox--show
      span.todo__task-txt(:class="{'todo__task-txt--checked': task.done}") {{ task.description }}
      span.todo__time-txt - {{ displayTime }}
    .button.button--hidden.button--state-remove(@click='deleteTask')
</template>

<script lang='ts'>
import { defineComponent, computed } from '@vue/composition-api';

// eslint-disable-next-line import/no-unresolved,import/extensions
import { ITask } from '../models/task';

export default defineComponent({
  name: 'Task',
  props: {
    task: {
      type: Object as () => ITask,
      default: <ITask>{},
    },
  },
  setup(props, { emit }) {
    const deleteTask = () => {
      emit('delete-task', props.task._id);
    };

    const updateTask = () => {
      emit('update-task', { id: props.task._id, done: !props.task.done });
    };

    const displayTime = computed(() => {
      const d = new Date();
      const nowTs = Math.floor(d.getTime() / 1000);
      const createdTs = Math.floor(new Date(props.task.createdAt).getTime() / 1000);
      const seconds = nowTs - createdTs;

      // more than two days
      if (seconds > 2 * 24 * 3600) {
        return 'a few days ago';
      }

      // a day
      if (seconds > 24 * 3600) {
        return 'yesterday';
      }

      if (seconds > 3600) {
        return 'a few hours ago';
      }

      if (seconds > 1800) {
        return 'Half an hour ago';
      }

      if (seconds > 60) {
        return `${Math.floor(seconds / 60)} minutes`;
      }

      return 'moments ago';
    });

    return {
      deleteTask, displayTime, updateTask,
    };
  },
});
</script>
