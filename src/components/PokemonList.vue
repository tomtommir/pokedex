<template>
	<section class="pokemon-item">
		<div v-if="loading" class="loading">
            <img src="/images/pokeball.svg" class='loader'>
        </div>
        <div v-else>
			<div v-if="error" class="error">
				Problem while listing pokemon list
			</div>
			<div v-else>
                <ul class="pokemon-list" id="infiniteScroll">
                    <li 
                        v-for="pokemon in pokemonList" 
                        :key="pokemon.id" 
                        @click="getInfoPokemon(pokemon.url,pokemon.name)"
                        :class="pokemon.class"
                        >
                        <div class="top">
                            <span class="name-mobile">{{ pokemon.name }}</span>
                            <span class="id">#{{ pokemon.id }}</span>
                        </div>
                        <div class="bottom">
                            <div class="left">
                                <span class="name">{{ pokemon.name }}</span>
                                <div class="types">
                                    <span 
                                        v-for="type in pokemon.types" 
                                        :key="type.slot"
                                        >
                                        {{ type.type.name }}
                                    </span>
                                </div>
                            </div>
                            <div class="right">
                                <img class="pokemon-img" :src="pokemon.img" :alt="pokemon.name" />
                            </div>
                        </div>
                    </li>
                    <div v-if="loadingScroll" class="loading">
                        <img src="/images/pokeball.svg" class='loader'>
                    </div>
                </ul>
			</div>
        </div>
	</section>
</template>

<script>
import axios from "axios"

export default {
	name: "PokemonList",
	props: ["search"],
    components: {
		
	},
	watch: {
        search: function(search) {
			this.getPokemonListSearch(search)
        }
	},
	data() {
		return {
			loading: false,
			loadingScroll: false,
            pokemonByLoading: 20,
            url: "https://pokeapi.co/api/v2/pokemon/?limit=20",
            urlSearch: "https://pokeapi.co/api/v2/pokemon/?limit=1200",
            isSearch: false,
            pokemonList: [],
            offsetElm: 1
		}
	},
	methods: {
        //Pour mettre enb forme correctement l'ID
        //rajoute autant de 0 possible suivant la taille
        padLeadingZeros(num, size) {
            var s = num+"";
            while (s.length < size) s = "0" + s;
            return s;
        },
        //On récupère l'image dispo du pokemon
        choosePokemonImg(sprites,key){
            if(sprites.other['official-artwork'].front_default == null){
                if(sprites.other.dream_world.front_default== null){
                    //on a pas d'image on cache le pokemon
                    this.pokemonList[key].class = "inactive"
                    this.pokemonList[key].img = ""
                }else{
                    this.pokemonList[key].img = sprites.other.dream_world.front_default
                }
            }else{
                this.pokemonList[key].img = sprites.other['official-artwork'].front_default
                
            }
        },
        //On récupère le contenu de chaque pokemon
        //pour mettre a jour pokemonList
        getPokemonListInfo(url,key){
            axios.get(url)
            .then(response => {
                //On vient retourner les infos voulus du pokemon dans l'objet pokemonList
                //Pour la class on prend le premier type comme base
                this.pokemonList[key].id = this.padLeadingZeros(response.data.id,4)
                this.pokemonList[key].types = response.data.types
                this.pokemonList[key].class = response.data.types[0].type.name
                //On regarde si il n'y a pas d'image
                this.choosePokemonImg(response.data.sprites,key)
                this.pokemonList[key].url = url
            })
            .catch(error => { 
                console.log(error)
            })
            
        },
        //On récupère la liste des pokemons
        //uniquement name et url
		getPokemonList(url){
            const self = this
            axios.get(url)
            .then(response => {
                //On parcours le resultat pour ajouter sur la liste en cours
                response.data.results.map(function(value){
                    self.pokemonList.push(value)
                })

                //maj de chaque pokemon avec des infos supplémentaire
                //type, id, class, img
                this.pokemonList.map(function(value, key) {
                    self.getPokemonListInfo(value.url,key)
                })
            })
            .catch(error => { 
                console.log(error)
            })
            .finally(() => ( this.loadingScroll = false ))
		},
        //Similaire à getPokemonList
        //sans utiliser d'url paramètre
        getPokemonListFirst(){
            const self = this
			this.loading = true
            axios.get(self.url)
            .then(response => {
                //On vient mettre la liste à jour
                this.pokemonList = response.data.results

                //maj de chaque pokemon avec des infos supplémentaire
                //type, id, class, img
                this.pokemonList.map(function(value, key) {
                    self.getPokemonListInfo(value.url,key)
                })
            })
            .catch(error => { 
                console.log(error)
            })
            .finally(() => ( this.loading = false ))
		},
        getPokemonListSearch(search){
            const self = this
            this.loading = true
            this.isSearch = true

            axios.get(this.urlSearch)
            .then(response => {
                //On vient mettre la liste à jour
                this.pokemonList = response.data.results.filter(pokemonList =>
                    pokemonList.name.toLowerCase().includes(search.toLowerCase())
                );

                //maj de chaque pokemon avec des infos supplémentaire
                //type, id, class, img
                this.pokemonList.map(function(value, key) {
                    self.getPokemonListInfo(value.url,key)
                })
            })
            .catch(error => { 
                console.log(error)
            })
            .finally(() => ( this.loading = false ))
        },
        getInfoPokemon(url,name){
            this.$parent.majData(name,url,'click')
        }
	},
    beforeMount() {
        //On lance la liste de pokemon de base
		this.getPokemonListFirst();
    },
    mounted() {
        //Chargement au scroll
        window.onscroll = () => {
            //On regarde si on arrive en base de page
            let bottomOfWindow = Math.round(document.documentElement.scrollTop + window.innerHeight) === document.documentElement.offsetHeight

            //On vient charger les nouveaux items
            //Si bottomOfWindow est true que l'on est pas en resultat de recherche
            if (bottomOfWindow && !this.isSearch) {
                this.loadingScroll = true
                let newOffset = this.offsetElm * this.pokemonByLoading
                let newUrl = "https://pokeapi.co/api/v2/pokemon/?offset="+newOffset+"&limit="+this.pokemonByLoading;
                this.getPokemonList(newUrl)
                this.offsetElm++
            }
        }
    }

}
</script>

<style scoped lang="scss">
    .pokemon-item{
        width: 100%;
        max-width: 2000px;
        margin: 0 auto;
        display: flex;
        flex-direction: column;
        row-gap: 20px;
        z-index: 1;
        transition: all 400ms ease-in-out;
        ul.pokemon-list{
            list-style: none;
            margin: 0;
            padding: 0;
            display: grid;
            grid-template-columns: repeat(3,1fr);
            column-gap: 20px;
            row-gap: 20px;
            margin-bottom: 20px;
            position: relative;
            li{
                display: flex;
                flex-direction: column;
                align-items: center;
                cursor: pointer;
                border-radius: 30px;
                background-image: url("../assets/background-pokemon.png");
                background-position: right bottom;
                background-repeat: no-repeat;
                background-size: 60%;
                padding: 20px;
                transition: all 400ms ease-in-out;
                .top{
                    display: flex;
                    width: 100%;
                    margin-bottom: 30px;
                    justify-content: space-between;
                    .name-mobile{
                        text-transform: capitalize;
                        color: white;
                        font-size: 20px;
                        font-weight: bold;
                        opacity: 0;
                        visibility: hidden;
                    }
                    .id{
                        width: fit-content;
                        font-size: 30px;
                        font-weight: 700;
                        text-align: right;
                        opacity: 0.15;
                        color: white;
                        font-family: 'Lalezar', cursive;
                    }
                }
                .bottom{
                    display: flex;
                    flex-direction: row;
                    width: 100%;
                    .left{
                        width: 50%;
                        display: flex;
                        flex-direction: column;
                        .name{
                            text-transform: capitalize;
                            color: white;
                            font-size: 30px;
                            font-weight: bold;
                            margin-bottom: 30px;
                        }
                        .types{
                            display: flex;
                            flex-direction: column;
                            row-gap: 15px;
                            margin-bottom: 30px;
                            span{
                                color:white;
                                text-transform: capitalize;
                                background-color: rgba(255, 255, 255, 0.2);
                                border-radius: 30px;
                                padding: 10px 20px;
                                display: inline-block;
                                width: fit-content;
                            }
                        }
                    }
                    .right{
                        width: 50%;
                        display: flex;
                        justify-content: center;
                        img{
                            transition: all 400ms ease-in-out;
                            width: 100%;
                            height: auto;
                        }
                    }
                }
                &:hover{
                    -webkit-box-shadow: inset 5px 5px 45px 9px rgba(0,0,0,0.3); 
                    box-shadow: inset 5px 5px 45px 9px rgba(0,0,0,0.3);
                    .bottom{
                        .right{
                            img{
                                transform: scale(1.05);
                            }
                        }
                    }
                }
            }
            .loading{
                position: absolute;
                bottom: 50px;
                left: 50%;
                transform: translateX(-50%);
                display: inherit;
            }
        }
    }
	.loading{
        position: relative;
        display: flex;
        justify-content: center;
		.loader{
			width: 200px;
			animation: spin 2s linear infinite;
			transform-origin: center;
		}
	}

    //////////////////////////////
	/////   Color List Type //////
	//////////////////////////////
    .normal{
        background-color: #A8A77A;
    }
    .fighting{
        background-color: #C22E28;
    }
    .flying{
        background-color: #A98FF3;
    }
    .poison{
        background-color: #A33EA1;
    }
    .ground{
        background-color: #E2BF65;
    }
    .rock{
        background-color: #B6A136;
    }
    .bug{
        background-color: #A6B91A;
    }
    .ghost{
        background-color: #735797;
    }
    .steel{
        background-color: #B7B7CE;
    }
    .fire{
        background-color: #EE8130;
    }
    .water{
        background-color: #6390F0;
    }
    .grass{
        background-color: #7AC74C;
    }
    .electric{
        background-color: #F7D02C;
    }
    .psychic{
        background-color: #F95587;
    }
    .ice{
        background-color: #96D9D6;
    }
    .dragon{
        background-color: #6F35FC;
    }
    .dark{
        background-color: #705746;
    }
    .fairy{
        background-color: #D685AD;
    }
    .unknown{
        background-color: #9ea382;
    }
    .shadow{
        background-color: #080c46;
    }
    .inactive{
        display: none !important;
    }

    ///////////////////////////
	/////   MediaQueries //////
	///////////////////////////
	@media screen and (max-width: 1100px) {
        .pokemon-item{
            ul.pokemon-list{
                grid-template-columns: repeat(2,1fr);
                column-gap: 20px;
                row-gap: 20px;
                margin-bottom: 20px;
                li{
                }
            }
        }
        
    }
    
    @media screen and (max-width: 760px) {
		.pokemon-item{
            ul.pokemon-list{
                grid-template-columns: repeat(1,1fr);
            }
        }
    }
      
    @media screen and (max-width: 500px) {
		.pokemon-item{
            ul.pokemon-list{
                li{
                    .top{
                        .id{
                            font-size: 20px;
                        }
                        .name-mobile{
                            opacity: 1;
                            visibility: visible;
                        }
                    }
                    .bottom{
                        flex-direction: column;
                        .left{
                            display: none;
                        }
                        .right{
                            width: 100%;
                            img{
                                max-width: 50%;
                            }
                        }
                    } 
                }
            }
        }
    }  

	///////////////////////////
	/////   Keyframes   ///////
	///////////////////////////
	@keyframes spin { 
		100% { 
			-webkit-transform: rotate(360deg); 
			transform:rotate(360deg); 
		} 
	}
</style>
