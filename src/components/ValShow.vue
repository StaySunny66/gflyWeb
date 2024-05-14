<template>
  <div class="data-statistic">

    <div class="value">{{ value }}</div>
    <div class="name">{{ name }}</div>
  </div>
</template>

<script setup>
const { props } = __VUE_SCRIPT_SETUP__
</script>

<style scoped>
.data-statistic {
  text-align: center;
}

.value {
  font-size: 24px;
  font-weight: bold;
}

.name {
  font-size: 18px;
}
</style>
