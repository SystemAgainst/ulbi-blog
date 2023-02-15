<template>
  <input :value="modelValue" @input="updateInput" type="text" placeholder="" />
</template>

<script>
export default {
  name: "AppInput",
  props: {
    modelValue: [String, Number],
  },
  setup(_, {emit}) {
    const updateInput = (event) => {
      emit('update:modelValue',  event.target.value);
    };

    return {updateInput};
  },
}
</script>
