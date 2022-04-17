<template>
    <ul>
        <li>
            <span class="title">Shape</span>
            <span class="content">{{ shape }}</span>
        </li>
        <li>
            <span class="title">height</span>
            <span class="content">{{ height }}</span>
        </li>
        <li>
            <span class="title">Weight</span>
            <span class="content">{{ weight }}</span>
        </li>
        <li>
            <span class="title">Abilities</span>
            <span class="content">{{ abilitiesTxt }}</span>
        </li>
        <li>
            <span class="title">Gender</span>
            <span class="content">
                <i class="ri-men-line"></i>{{ genderM }}
                <i class="ri-women-line"></i>{{ genderW }}
                <i class="ri-genderless-line"></i>
            </span>
        </li>
    </ul>
</template>

<script>
import axios from "axios"

export default {
	name: "AboutContent",
    props: ["idPokemon"],
	watch: {
        idPokemon: function(idPokemon) {
			this.getSize(idPokemon)
			this.getShapeGender(idPokemon)
        }
	},
	data() {
		return {
			shape: "",
			height: "",
			weight: "",
			abilitiesTxt: "",
			abilities: [],
			gender: [],
			genderW: "",
			genderM: "",
			eggGroups: "",
			eggCycle: ""
		}
	},
	methods: {
        getSize(id){
            const self = this

            axios.get("https://pokeapi.co/api/v2/pokemon/"+id)
            .then(response => {
                this.height = String(response.data.height*10) + " cm"
                this.weight = String(response.data.weight/10) + " kg"
                this.abilities = response.data.abilities
                //On met le tableau d'abilités dans un string
                this.abilities.forEach(function(item,index,array){
                    //On regarde le derniere itération de la boucle
                    if (index === array.length - 1){ 
                        self.abilitiesTxt += item.ability.name
                    }else{
                        self.abilitiesTxt += item.ability.name + ", "
                    }
                })
            })
            .catch(() => { 
                this.error = true
            })
            .finally(() => (this.loading = false))
        },
        //On récupère le shape et le genre (score sur 8, si égale à -1 alors pas de genre)
		getShapeGender(id){
            axios.get("https://pokeapi.co/api/v2/pokemon-species/"+id)
            .then(response => {
                this.shape = response.data.shape.name
                if(response.data.gender_rate == -1){
                    this.genderW = 0
                    this.genderM = 0
                }else{
                    this.genderW = (response.data.gender_rate / 8) * 100
                    this.genderM = ((8 - response.data.gender_rate) / 8) * 100
                }
                
            })
            .catch(() => { 
                this.error = true
            })
            .finally(() => (this.loading = false))
		},
	}
}
</script>