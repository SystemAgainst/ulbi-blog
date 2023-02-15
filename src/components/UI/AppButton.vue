<template>
  <button class="btn" :class="color" @click="$emit('action')">
    <slot />
  </button>
</template>

<script>
export default {
  name: "AppButton",
  emits: ['action'],
  props: {
    color: {
      type: String,
      required: true,
      default: "",
      validator(value) {
        return ["", "primary", "danger", "warning"].includes(value);
      },
    },
  },
}
</script>
