<template>
  <div class="home-page">
    <div class="home-page__section">
        <Fractional></Fractional>
    </div>
  </div>
</template>

<script>
    import Fractional from './fractional.vue';

    export default {
        name: 'home-page',
        components: {
            Fractional,
        }
    };
</script>

<style lang="scss">
    .home-page {
        height: 100%;

        &__section {
            position: relative;
            height: 100%;
        }
    }
</style>