<template>
	<i 
		class="ri-search-line search-toggle"
		@click="searchToggle()"
		:class="{active: isSearchActive}"
		></i>
	<div 
		class="search-container"
		:class="{active: isSearchActive}"
		>
		<i 
			class="ri-search-line search-button"
			@click="searchPokemon()"></i>
		<input 
			type="text" 
			class="search-bar" 
			v-model="search" 
			placeholder="Search a pokémon"
			v-on:keyup.enter="searchPokemon()"/>
		<i 
			class="ri-close-line search-reset" 
			@click="searchReset()"></i>
	</div>
	<h1>Pokédex</h1>
	<div class="container-list">
		<PokemonList 
			ref="pokemonList"
			:search="searchList"
		/>
		<!-- <PokemonInfo
			:name="namePokemon"
			:url="urlPokemon"
			:clicked="clickedList"
		/> -->
	</div>
</template>

<script>
// import PokemonInfo from "./components/PokemonInfo"
import PokemonList from "./components/PokemonList"
import $ from 'jquery'

export default {
	name: "App",
	components: {
		// PokemonInfo,
		PokemonList,
	},
	data() {
		return {
			namePokemon: "Pikachu",
			urlPokemon: "https://pokeapi.co/api/v2/pokemon/pikachu",
			search: "",
			searchList: "",
			clickedList: false,
			isSearchActive: false
		}
	},
	methods:{
		searchToggle(){
			//Trigger de la recherche
			//On change la valeur de isSearchActive
			if(this.isSearchActive == true){
				this.isSearchActive = false
			}else{
				this.isSearchActive = true
			}
			
		},
		majData(name,url,type){
			this.namePokemon = name;
			this.urlPokemon = url;
			if(type === 'click'){
				//On est sur un click sur la liste
				//MAJ de la pokemonInfo
				this.clickedList = true
				$('html, body').animate({scrollTop:$('#pokemon-info').position().top}, 'slow');
			}else if(type === 'search'){
				//On est sur une recherche sur la liste
				//MAJ de la pokemonList
				this.searchList = this.search
			}
		},
		searchPokemon(){
			if(this.search == ""){
				return false
			}else{
				this.majData(this.search,"https://pokeapi.co/api/v2/pokemon/"+this.search,'search')
			}
		},
		searchReset(){
			//On vide le champ recherche
			this.search = ""
			//On initialise les champs Pour le component PokemonInfo
			this.namePokemon = "Pikachu";
			this.urlPokemon = "https://pokeapi.co/api/v2/pokemon/pikachu";
			//On initialise la liste en appelant la fonction de PokemonList
			this.$refs.pokemonList.getPokemonListFirst()
			//On reset le offset du Infinite Scroll
			this.$refs.pokemonList.offsetElm = 1
			//On reset la recherche
			this.$refs.pokemonList.isSearch = false
		}
	},
	mounted(){

	}
}
</script>

<style lang="scss">
	@import url('https://fonts.googleapis.com/css2?family=Lalezar&display=swap');
	@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;700&display=swap');

	#app{
		padding: 30px;
		&:before{
			content:'';
			top: 0;
			right: 0;
			position: fixed;
			display: block;
			width: 15rem;
			height: 16.688rem;
			background: url("./assets/background-pokeball.png");
			background-size: cover;
		}
		*{
			box-sizing: border-box;
			font-family: 'Poppins', sans-serif;
		}
		.search-toggle{
			position: fixed;
			right: 10px;
			top: 40px;
			font-size: 30px;
			cursor: pointer;
			transition: all 400ms ease-in-out;
			z-index: 1;
			padding: 20px 30px;
			background-color: #f5f5f7;
			border-radius: 50px;
			display: flex;
			z-index: 3;
			&:before{
				transition: all 400ms ease-in-out;
			}
			&:hover{
				&:before{
					transform: scale(1.4);
				}
			}
			&.active{
				&:before{
					content: "\eb99";
				}
			}
		}
		.search-container{
			width: 400px;
			top: 40px;
			left: 50%;
			margin-left: -200px;
			position: fixed;
			display: none;
			z-index: 3;
			&.active{
				display: flex;
			}
			.search-button{
				cursor: pointer;
				position: absolute;
				left: 15px;
				top: 50%;
				transform: translateY(-50%);
			}
			.search-reset{
				cursor: pointer;
				position: absolute;
				right: 15px;
				top: 50%;
				transform: translateY(-50%);
			}
			.search-bar{
				width: 100%;
				padding: 10px 40px;
				border: none;
				border-radius: 20px;
				border: 2px solid #f5f5f7;
				transition: all 400ms ease-in;
				&:focus-visible{
					outline: none;
				}
				&:focus,&:active{
					outline: none;
					border: 2px solid #2b2b2b;
				}
			}
		}
	}
	h1{
		font-family: 'Lalezar', cursive;
		font-weight: 700;
		text-align: left;
		margin: 30px 0px 0px 0px;
	}
	
	.container-list{
		width: 100%;
        margin: 50px auto;
		display: flex;
		column-gap: 20px;
	}
	
	@media screen and (max-width: 1100px) {
		.app{
			margin-top: 20px;
		}
		.nav{
			column-gap: 20px;
		}
		.container-list{
			flex-direction: column;
			display: flex;
			row-gap: 20px;
		}
	}

	@media screen and (max-width: 500px) {
		.search-container{
			width: 100%;
		}

	}

</style>
