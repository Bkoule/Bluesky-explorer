<template>
    <div class="post-card" :class="{'selected' : isSelected}" >
        <button
            @click="$emit('toggle')"
            :class="['select-button', {'selected': isSelected }]">
            {{ isSelected ? '✓' : '+' }}
        </button>
        <div class="post-content" >
            <h3 class="username" >{{ username }}</h3>
            <p class="content" >{{ content }}</p>
        </div>
        <span class="timestamp" >{{ timestamp }}</span>
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
        background-color: #192734;
        display: flex;
        flex-direction: column;
        position: relative;
        transition: all 0.3s ease;
        border: 1px solid #38444d;
        padding: 15px;
        border-radius: 12px;
        height: 100%;
        box-sizing: border-box;
    }

    .post-card:hover {
        background-color: #22303c;
    }

    .post-card.selected {
        border-color: #1DA1F2
    }

    .select-button {
        position: absolute;
        top: 12px;
        right: 12px;
        width: 30px;
        height: 30px;
        border-radius: 50%;
        border: none;
        background-color: #1DA1F2;
        color: white;
        font-size: 18px;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;
    }

    .select-button:hover {
        background-color: #1991db;
    }

    .post-content {
        margin-right: 40px;
    }

    .username {
        color: white;
        font-size: 15px;
        font-weight: bold;
    }

    .content {
        color: white;
        font-size: 13px;
    }

    .timestamp {
        color: #8899A6;
        font-size: 12px;
        align-self: flex-start;
        margin-top: auto;
    }

</style>