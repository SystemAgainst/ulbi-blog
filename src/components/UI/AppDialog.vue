<template>
  <div class="modal-backdrop" v-if="show" @click.stop="hideDialog">
      <div @click.stop class="modal">
        <slot />
      </div>
  </div>

</template>

<script>
export default {
  name: "AppDialog",
  emits: ['update:show'],
  props: {
    show: {
      type: Boolean,
      required: true,
      default: false,
    },
  },
  setup(_, {emit}) {
    const hideDialog = () => {
      emit('update:show', false);
    };

    return {
      hideDialog,
    };
  },
}
</script>
