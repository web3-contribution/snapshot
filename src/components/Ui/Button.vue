<script setup>
import { defineProps } from 'vue';

defineProps({
  loading: Boolean,
  type: String,
  disable: {
    type: Boolean,
    default: true
  }
});
</script>

<template>
  <button
    :type="type || 'button'"
    class="button"
    :disabled="disable ? loading : false"
  >
    <UiLoading v-if="loading" />
    <slot v-else />
  </button>
</template>

<style scoped lang="scss">
.button {
  border: 1px solid var(--border-color);
  background-color: transparent;
  color: var(--link-color);
  border-radius: 23px;
  padding: 0 24px;
  outline: none;
  line-height: 46px;
  height: 46px;
  font-size: 18px;

  &.button--submit {
    color: white;
    background-color: var(--primary-color);
    border: 1px solid var(--primary-color);

    &:hover {
      color: white;
      background-color: var(--primary-color);
      border: 1px solid var(--primary-color);
    }

    &:disabled {
      color: var(--link-color) !important;
      border: 1px solid var(--border-color);
      background-color: var(--border-color);
    }
  }

  &.button--active {
    border-color: var(--link-color) !important;
  }

  &:hover {
    color: var(--link-color);
    border-color: var(--link-color);
  }

  &:disabled {
    color: var(--border-color) !important;
    cursor: not-allowed;
  }
}
</style>
