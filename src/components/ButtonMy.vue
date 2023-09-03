<template>
    <button class="v-button" v-on:click.prevent="executeMethod">
        <slot></slot>
    </button> 
</template>

<script>
    export default{
        name: "ButtonMy",
        methods:{
            executeMethod() {
                this.$emit('executeMethod');
            },
        },
    }
</script>

<style scoped>
    .v-button {
        width: 100px;
        height: 100px;
        background: white;
        border-radius: 10%;
        cursor: pointer;
        color: black;
    }
</style>
