<template>
    <div class="post-card" >
        <h3>{{ username }}</h3>
        <p>{{ content }}</p>
        <button
            @click="$emit('toggle')"
            :class="['select-button', {'selected': isSelected }]">
            fav 
        </button>
        <span>{{ timestamp }}</span>
    </div>
</template>

<script>
    export default {
        name: 'PostCard',
        props: {
            username: String,
            content: String,
            timestamp: String,
            isSelected : Boolean
        }
    }
</script>

<style scoped>
    .post-card {
        border: 1px solid black;
        padding: 10px;
        border-radius: 5px;
    }

    .select-button {
        cursor: pointer;
    }

    .select-button.selected {
        background-color: blueviolet;
    }

</style>