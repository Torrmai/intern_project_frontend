<template>
    <div class="search-area">
        <div>
            <input placeholder="Search me :)">
            <button id="search-btn">Go!</button>
        </div>
    </div>
</template>
<script>
export default {
    name:'search'
}
</script>
<style>
.search-area{
    display: flex;
    flex-direction: row;
    justify-content: center;
}
#search-btn{
    margin-left: 10px;
}
</style>