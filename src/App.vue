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
		padding: 0 30px;
		*{
			box-sizing: border-box;
			font-family: $poppins;
		}
		h1{
			font-family: $lalezar;
			font-weight: 700;
			text-align: left;
			margin: 50px 0px 0px 0px;
		}
		&:before{
			content:'';
			top: 0;
			right: 0;
			position: fixed;
			display: block;
			width: 15rem;
			height: 16.688rem;
			background: url("@/assets/background-pokeball.png");
			background-size: cover;
		}
	}
	@media screen and (max-width: $big-phone) {
		#app{
			h1{
				margin-top: 20px;
			}
			&:before{
				display: none;
			}			
		}
	}
</style>