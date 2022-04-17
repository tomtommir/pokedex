<template>
    <div v-if="!isLoaded" class="loading">
        <img src="/images/pokeball.svg" class='loader'>
    </div>
</template>


<script>
export default {
    name: "LoaderContainer",
	data() {
		return { 
            isLoaded: false
        } 
    },
    mounted() {
        document.onreadystatechange = () => {
            if(document.readyState == "complete"){
                this.isLoaded = true
            }
        }
    }
}
</script>

<style scoped lang="scss">
    .loading{
        position: relative;
        display: flex;
        justify-content: center;
		.loader{
			width: 200px;
			animation: $spin;
			transform-origin: center;
		}
	}
</style>