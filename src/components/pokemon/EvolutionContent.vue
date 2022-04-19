<template>
    <ul>
        <li
            v-for="pokemon in evolutionPokemon" 
            :key="pokemon.id">
            <img 
                class="pokemon-img" 
                :src="pokemon.img" 
                :alt="pokemon.name" 
                />
            <span class="title">{{ pokemon.name }}</span>
        </li>
    </ul>
</template>

<script>
import axios from "axios"

export default {
	name: "EvolutionContent",
    props: ["idPokemon"],
	watch: {
        idPokemon: function(idPokemon) {
			this.getUrlEvolution(idPokemon)
        }
	},
	data() {
		return {
			urlEvolution: "",
			evolution: [],
			evolutionPokemon: [],
		}
	},
	methods: {
        //On récupère l'URL de la chaine d'Evolution
        getUrlEvolution(id){
            const self = this

            axios.get("https://pokeapi.co/api/v2/pokemon-species/"+id)
            .then(response => {
                this.urlEvolution = response.data.evolution_chain.url
                this.getEvolution(self.urlEvolution)
            })
            .catch(() => { 
                this.error = true
            })
            .finally(() => (this.loading = false))
        },
        //On récupère l'Evolution du pokemon
        getEvolution(url){
            const self = this

            axios.get(url)
            .then(response => {
                this.evolution = response.data.chain

                //On récupère le nom et l'url des évolutions
                this.mapEvolution(self.evolution)

                //On maj l'url, ID et img des évolutions
                this.evolutionPokemon.map(function(value, key) {
                    self.getPokemonInfoEvolution(value.url,key)
                })
            })
            .catch(() => { 
                this.error = true
            })
            .finally(() => (this.loading = false))
        },
        //On vient créer un array d'objet des évolutions
        //On appelle la fonction recursive tant que la valeur evolves_to n'est pas vide
        mapEvolution(evol){
            const Pokemon = {}
            Pokemon.name = evol.species.name
            Pokemon.url = evol.species.url
            if(!evol.evolves_to.length){
                this.evolutionPokemon.push(Pokemon)
                return
            }else{
                this.evolutionPokemon.push(Pokemon)
                this.mapEvolution(evol.evolves_to[0])   
            }
        },
        //On récupère l'URL, ID et l'IMG des pokémons des évolutions
        getPokemonInfoEvolution(url,key){
            axios.get(url)
            .then(response => {
                this.evolutionPokemon[key].id  = response.data.id
                this.evolutionPokemon[key].url = "https://pokeapi.co/api/v2/pokemon/" + response.data.id

                //On récupère l'img des évolutions
                this.getPokemonImg("https://pokeapi.co/api/v2/pokemon/" + response.data.id,key)
            })
            .catch(() => { 
                this.error = true
            })
            .finally(() => (this.loading = false))
        },
        //On récupère le contenu de chaque pokemon pour l'évolution
        //pour mettre a jour evolutionPokemon
        getPokemonImg(url,key){
            axios.get(url)
            .then(response => {
                //On regarde si il n'y a pas d'image
                this.choosePokemonImg(response.data.sprites,key)
            })
            .catch(error => { 
                console.log(error)
            })
            
        },
        //On récupère l'image dispo du pokemon
        choosePokemonImg(sprites,key){
            if(sprites.other['official-artwork'].front_default == null){
                if(sprites.other.dream_world.front_default== null){
                    //on a pas d'image on cache le pokemon
                    this.evolutionPokemon[key].class = "inactive"
                    this.evolutionPokemon[key].img = ""
                }else{
                    this.evolutionPokemon[key].img = sprites.other.dream_world.front_default
                }
            }else{
                this.evolutionPokemon[key].img = sprites.other['official-artwork'].front_default
                
            }
        },
	},
    mounted(){
        this.getUrlEvolution(this.idPokemon)
    }
}
</script>

<style lang="scss" scoped>
    ul{
        margin: 0;
        padding: 30px 0;
        list-style: none;
        display: flex;
        column-gap: 100px;
        li{
            display: flex;
            row-gap: 20px;
            padding: 0;
            flex-grow: 1;
            flex-direction: column;
            align-items: center;
            img{
                width: 100%;
            }
            .title{
                text-transform: capitalize;
                font-size: 17px;
            }
        }
    }

    ///////////////////////////
	/////   MediaQueries //////
	///////////////////////////
    @media screen and (max-width: $tablet) {
        ul{
            column-gap: 50px;
            li{

            }
        }
    }

	@media screen and (max-width: $phone) {
        ul{
            display: flex;
            flex-direction: column;
            row-gap: 40px;
            li{
                row-gap: 10px;
                img{
                    max-width: 50%;
                    margin: 0 auto;
                }
            }
        }
    }
</style>