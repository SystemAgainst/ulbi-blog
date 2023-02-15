<template>
  <!-- ключ - уникальный. В списка подразумевается, что опции уникальны -->
  <div class="form-control">
    <select :value="modelValue" @change="changeOption">
      <option disabled value="">Виберите из списка</option>
      <option
          v-for="option in options"
          :key="option.value"
          :value="option.value"
      >
        {{ option.name }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: "AppSelect",
  emits: ['update:model-value'],
  props: {
    modelValue: [String],
    options: {
      type: Array,
      default: () => [],
    },
  },
  setup(props, {emit}) {
    const changeOption = (event) => {
      emit('update:model-value', event.target.value);
    };
    return {changeOption};
  },
}
</script>
