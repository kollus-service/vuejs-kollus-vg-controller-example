<script setup>
import KollusPlayer from './components/KollusPlayer.vue'
import VgController from './components/VgController.vue'
</script>

<template>
    <main>
        <div>
            <KollusPlayer/>
        </div>
        <div>
            <VgController/>
        </div>
    </main>
</template>

<style scoped>
main {
    display: flex;
    height: 100vh;
    width: 100%;
}
main div {
    padding-right: 20px;
    padding-left: 20px;
    width: 50%;
    flex: 1;
}

</style>
