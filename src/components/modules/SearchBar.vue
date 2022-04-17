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
            isSearchActive: false,
            search: ""
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
                //On envoie au component parent le resultat de la recherche
                this.$emit('search-pokemon', { 
                        content: this.search,
                        url: "https://pokeapi.co/api/v2/pokemon/"+this.search,
                        type: 'search'
                    }
                )
			}
		},
		searchReset(){
			//On vide le champ recherche
			this.search = ""

            //On envoie au component parent le reset de la recherche
            this.$emit('reset-pokemon')
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
        transition: $transition;
        z-index: 1;
        padding: 30px;
        background-color: $white-cream-transparent;
        border-radius: 50px;
        display: flex;
        z-index: 3;
        &:before{
            transition: $transition;
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
            border: 2px solid $white-cream-transparent;
            transition: $transition;
            &:focus-visible{
                outline: none;
            }
            &:focus,&:active{
                outline: none;
                border: 2px solid $dark-grey;
            }
        }
    }
    @media screen and (max-width: $big-phone) {
    #app{
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