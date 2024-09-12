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

<style>

</style>