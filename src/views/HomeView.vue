<template>
	<SearchBar 
		@search-pokemon="searchPokemon"
		@reset-pokemon="resetPokemon"
	/>
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
import PokemonList from "@/components/home/PokemonList"
import SearchBar from "@/components/modules/SearchBar"
import $ from 'jquery'
export default {
	name: "HomeView",
	components: {
		PokemonList,
		SearchBar,
	},
	data() {
		return {
			namePokemon: String,
			urlPokemon: String,
			searchList: "",
			clickedList: false,
			isSearchActive: false
		}
	},
	methods:{
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
				this.searchList = name
			}
		},
		//On récupère le resultat de la recherche du component SearchBar
        searchPokemon(payload) {
            this.majData(payload.content,payload.url,payload.type)
        },
		//On reset la recherche
		resetPokemon(){
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

<style scoped lang="scss">
	
	.container-list{
		width: 100%;
        margin: 50px auto;
		display: flex;
		column-gap: 20px;
	}
	
	@media screen and (max-width: $tablet) {
		.nav{
			column-gap: 20px;
		}
		.container-list{
			flex-direction: column;
			display: flex;
			row-gap: 20px;
		}
	}
	
</style>