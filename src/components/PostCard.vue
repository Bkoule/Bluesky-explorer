<template>
    <div class="post-card" >
        <h3>{{ username }}</h3>
        <p>{{ content }}</p>
        <span>{{ timestamp }}</span>
    </div>
</template>

<script>
    export default {
        name: 'PostCard',
        props: {
            username: String,
            content: String,
            timestamp: String
        }
    }
</script>

<style scoped>
    .post-card {
        border: 1px solid black;
        padding: 10px;
        border-radius: 5px;
    }
</style>