<template>
    <div class="home">
        <Hero />
        <Operator />
        <p>Powered by <a href="https://gemini.google.com/" class="fw-800">gemini</a></p>
    </div>
</template>

<script>
import Hero from './hero.vue';
import Operator from './operator.vue';

    export default {
    components: { Hero, Operator }
}
</script>

<style scoped>
.home {
    height: 100%;
    display: flex;
    flex-direction: column;
    padding: 50px;
}

@media (max-width: 750px) {
    .home {
        padding: 20px;
    }
}
</style>