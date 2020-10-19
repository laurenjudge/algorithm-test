<template>
    <div class="top-bar bg-secondary">
        This assignment is completed by: {{ name }}
    </div>
</template>

<script>
    export default {
        name: 'top-bar',
        data: () => ({
            name: 'Lauren Judge'
        })
    }
</script>

<style lang="scss" scoped>
    .top-bar {
        text-align: center;
        color: #fff;
        font-size: 0.8rem;
        padding: 0.5rem 1rem;
    }
</style>