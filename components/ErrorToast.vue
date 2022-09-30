<template>
  <div class="toast" id="error">
    <div class="alert alert-error">
      <div>
        <span id="error_msg">{{ message }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["message"],
};
</script>

<style></style>
