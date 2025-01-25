<script setup lang="js">
const { preloader } = defineProps({
  preloader: Boolean
})
</script>

<template>
    <div v-if="preloader" class="preloader flex-column justify-content-center align-items-center">
        please wait...
    </div>
</template>

<style scoped>

</style>