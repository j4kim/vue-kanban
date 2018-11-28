<template>
    <div class="card-content">
        <div class="card-info">
            <strong>{{ card.title }}</strong>
            <div>{{ card.id }}</div>
        </div>
        <div class="card-image">
            <img :src="'http://i.pravatar.cc/40?img=' + card.id" />
        </div>
    </div>
</template>

<script>
    export default {
        props: ['card']
    }
</script>

<style lang='scss'>
    .card-content{
        display: flex;

        img {
            border-radius: 20px;
        }
    }
</style>
