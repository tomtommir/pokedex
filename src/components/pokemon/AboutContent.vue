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
    </ul>
    <h3>Breeding</h3>
    <ul>
        <li>
            <span class="title">Gender</span>
            <span class="content">
                <span class="men" v-if="genderM != 0">
                    <i class="ri-men-line"></i>
                    {{ genderM }} %
                </span>
                <span class="women" v-if="genderW != 0">
                    <i class="ri-women-line"></i>
                    {{ genderW }} %
                </span>
                <span class="genderless" v-if="genderM == 0 && genderW == 0">
                    <i class="ri-genderless-line"></i>
                </span>
            </span>
        </li>
        <li>
            <span class="title">Egg groups</span>
            <span class="content">{{ eggGroupsTxt }}</span>
        </li>
        <li>
            <span class="title">Hatch time</span>
            <span class="content">{{ hatchCounter }} steps</span>
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
            this.getShapeGenderEgg(idPokemon)
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
			eggGroups: [],
			eggGroupsTxt: "",
			hatchCounter: "",
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
                        self.abilitiesTxt += self.capitalize(item.ability.name)
                    }else{
                        self.abilitiesTxt += self.capitalize(item.ability.name) + ", "
                    }
                })
            })
            .catch(() => { 
                this.error = true
            })
            .finally(() => (this.loading = false))
        },
        //On récupère le shape, le genre, EggGroup, temps d'écolision
		getShapeGenderEgg(id){
            const self = this

            axios.get("https://pokeapi.co/api/v2/pokemon-species/"+id)
            .then(response => {
                this.shape = this.capitalize(response.data.shape.name)

                //On met en % le genre (score sur 8, si égale à -1 alors pas de genre)
                if(response.data.gender_rate == -1){
                    this.genderW = 0
                    this.genderM = 0
                }else{
                    this.genderW = (response.data.gender_rate / 8) * 100
                    this.genderM = ((8 - response.data.gender_rate) / 8) * 100
                }

                this.eggGroups = response.data.egg_groups
                //On met le tableau des egg groups dans un string
                this.eggGroups.forEach(function(item,index,array){
                    //On regarde le derniere itération de la boucle
                    if (index === array.length - 1){ 
                        self.eggGroupsTxt += self.capitalize(item.name)
                    }else{
                        self.eggGroupsTxt += self.capitalize(item.name) + ", "
                    }
                })

                //Score du Compteur d'éclosion est égale à 255 * (hatch_count + 1)
                this.hatchCounter = 255 * (response.data.hatch_counter + 1)
                
            })
            .catch(() => { 
                this.error = true
            })
            .finally(() => (this.loading = false))
		},
        //Permettre de mettre la première lettre en majuscule
        capitalize(s) {
            if (typeof s !== 'string') return ''
            return s.charAt(0).toUpperCase() + s.slice(1)
        }

	},
    mounted() {
        //On regarde si idPokemon est défini pour pas le lancer au tout début 
        if(!isNaN(this.idPokemon)){
            this.getSize(this.idPokemon)
            this.getShapeGenderEgg(this.idPokemon)
        }
    }
}
</script>

<style lang="scss" scoped>
    h3{
        font-weight: 500;
        font-size: 20px;
        margin: 30px 0 0 0;
    }
    ul{
        margin: 0;
        padding: 30px 0 0 0;
        list-style: none;
        li{
            display: flex;
            column-gap: 20px;
            padding: 0 0 15px 0;
            .title{
                min-width: 120px;
                opacity: 0.6;
                font-size: 15px;
            } 
            .content{
                font-weight: 500;
                display: flex;
                column-gap: 15px;
                font-size: 17px;
                i{
                    &.ri-women-line{
                        color: $women;
                    }
                    &.ri-men-line{
                        color: $men;
                    }
                    &.ri-genderless-line{
                        
                    }
                }
            }   
        }
    }

    ///////////////////////////
	/////   MediaQueries //////
	///////////////////////////
	@media screen and (max-width: $phone) {
        h3{
            font-size: 18px;
        }
        ul{
            li{
                .title{
                    font-size: 14px;
                    min-width: 100px;
                } 
                .content{
                    font-size: 15px;
                }
            }
        }
    }
</style>