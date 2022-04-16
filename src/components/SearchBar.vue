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
			@click="searchReset()"
			title="Reset search"></i>
	</div>
</template>


<script>
export default {
    name: "SearchBar",
	data() {
		return { 
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
    mounted() {
        
    }
}
</script>

<style scoped lang="scss">
    .search-toggle{
        position: fixed;
        right: 10px;
        top: 40px;
        font-size: 30px;
        cursor: pointer;
        transition: all 400ms ease-in-out;
        z-index: 1;
        padding: 30px;
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
        top: 50px;
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
    @media screen and (max-width: 760px) {
    #app{
        h1{
            margin-top: 20px;
        }
        &:before{
            display: none;
        }
        .search-toggle {
            right: 20px;
            top: 20px;
            font-size: 25px;
            padding: 15px;
            &:hover{
                &:before{
                    transform: initial;
                }
            }
        }
        .search-container {
            width: 80%;
            top: 27px;
            left: 0;
            margin: 0;
            padding: 0 20px;
            .search-reset {
                right: 30px;
            }
            .search-button{
                left: 30px;
            }
        }
    }
    .search-container{
        width: 100%;
    }
}
</style>