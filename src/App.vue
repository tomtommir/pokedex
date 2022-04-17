<template>
	<!-- <nav>
		<router-link to="/">Pokedex</router-link> |
		<router-link to="/pokemon">Pokemon</router-link>
	</nav> -->
  	<router-view/>
</template>
<style lang="scss">
	#app {
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		color: $black;
		*{
			box-sizing: border-box;
			font-family: $poppins;
		}
		h1{
			font-family: $poppins;
			font-weight: 700;
			text-align: left;
			font-size: 40px;
			margin: 50px 0px 0px 0px;
		}
	}
	@media screen and (max-width: $big-phone) {
		#app{
			h1{
				margin-top: 20px;
			}			
		}
	}
</style>