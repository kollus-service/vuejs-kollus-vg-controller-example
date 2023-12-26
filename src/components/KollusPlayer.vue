<script>

export default {
    data() {
        return {
            name: 'kollus video player : VideoController Example',
            urlBase: 'https://v.kr.kollus.com/s?autoplay',
            jwt: 'JWT TOKEN 입력하세요',
            custom_key: 'custom_key 입력하세요',
        };
    },
    computed: {
        videoUrl() {
            return `${this.urlBase}&jwt=${this.jwt}&custom_key=${this.custom_key}`;
        }
    }
}

</script>

<template>
    <h1>{{ name }} </h1>
    <iframe id="kollus-player" :src="videoUrl" allow="autoplay;encrypted-media;fullscreen" width="1024" height="580" allowfullscreen="" webkitallowfullscreen="" mozallowfullscreen="" style="display: block;"></iframe>
</template>

<style scoped>
#kollus-player {
    width: 100%;
}
</style>
