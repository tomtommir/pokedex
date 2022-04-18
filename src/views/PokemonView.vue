<template>
	<div class="container-pokemon"
		:class="classPokemon"
		>
		<div class="back">
			<i class="ri-arrow-left-line"></i>
		</div>
		<div class="top">
			<div class="left">
				<h1>{{ name }}</h1>
				<div class="types">
					<span 
						v-for="type in types" 
						:key="type.slot"
						>
						{{ type.type.name }}
					</span>
				</div>
			</div>
			<div class="right">
				<span class="id">#{{ padLeadingZeros(id,4) }}</span>
			</div>
		</div>
		<div class="center">
			<img 
				:src="img" 
				:alt="name"
			/>
		</div>
		<div class="bottom">
			<TabModule class="" :tabList="tabList">
				<template v-slot:tabPanel-1>
					<AboutContent
						:idPokemon="id"
					/>
				</template>
				<template v-slot:tabPanel-2>
					<StatsContent
						:idPokemon="id"
					/> 
				</template>
				<template v-slot:tabPanel-3>
					<EvolutionContent
						:idPokemon="id"
					/>
				</template>
			</TabModule>
		</div>
	</div>
</template>

<script>
import axios from "axios"
import AboutContent from "@/components/pokemon/AboutContent.vue"
import StatsContent from "@/components/pokemon/StatsContent.vue"
import EvolutionContent from "@/components/pokemon/EvolutionContent.vue"
import TabModule from "@/components/modules/TabModule.vue"

export default {
	name: "PokemonView",
	components: {
		AboutContent,
		StatsContent,
		EvolutionContent,
		TabModule
	},
	data() {
		return {
			id: Number,
			name: "",
			classPokemon: "",
			types:[],
			tabList:["About","Base stats","Evolution"],
			img: "",
			url: "https://pokeapi.co/api/v2/pokemon/29",
			loading: false,
			error: false
		}
	},
	methods:{
		//Pour mettre enb forme correctement l'ID
        //rajoute autant de 0 possible suivant la taille
        padLeadingZeros(num, size) {
            var s = num+"";
            while (s.length < size) s = "0" + s;
            return s;
        },
        //On récupère l'image dispo du pokemon
        choosePokemonImg(sprites){
            if(sprites.other['official-artwork'].front_default == null){
                if(sprites.other.dream_world.front_default== null){
                    //on a pas d'image on cache le pokemon
                    this.classPokemon = "inactive"
                    this.img = ""
                }else{
                    this.img = sprites.other.dream_world.front_default
                }
            }else{
                this.img = sprites.other['official-artwork'].front_default
            }
        },
		getPokemonInfo(url){
			this.loading = true
			this.error = false
			setTimeout(()=>{
				axios.get(url)
				.then(response => {
					this.id = response.data.id
					this.name = response.data.name
					this.types = response.data.types
                	this.classPokemon = response.data.types[0].type.name
					this.choosePokemonImg(response.data.sprites)
				})
				.catch(() => { 
					this.error = true
				})
				.finally(() => (this.loading = false))
			},500);
		},
	},
	mounted(){
		this.getPokemonInfo(this.url)
	}
}
</script>

<style scoped lang="scss">
	.container-pokemon{
		display: flex;
		flex-direction: column;
		.back{
			padding: 30px 30px 0 30px;
			i{
				font-size: 30px;
				color: $white;
				cursor: pointer;
			}
		}
		.top{
			display: flex;
			flex-direction: row;
			justify-content: space-between;
    		align-items: center;
			padding: 0 30px;
			.left{
				h1{
					text-transform: capitalize;
					color: $white;
					font-size: 30px;
					font-weight: bold;
					margin-bottom: 30px;
					font-family: $poppins;
				}
				.types{
					display: flex;
					flex-direction: row;
					column-gap: 15px;
					margin-bottom: 30px;
					span{
						color: $white;
						text-transform: capitalize;
						background-color: $grey-transparent;
						border-radius: 30px;
						padding: 10px 20px;
						display: inline-block;
						width: fit-content;
					}
				}
			}
			.right{
				.id{
					width: fit-content;
					font-size: 30px;
					font-weight: 700;
					text-align: right;
					opacity: 0.15;
					color: $white;
					font-family: $lalezar;
				}
				
			}
		}
		.center{
    		text-align: center;
			position: relative;
			background-image: url("../assets/background-pokemon.png");
			background-position: right bottom;
			background-repeat: no-repeat;
			z-index: 3;
			img{
				margin-bottom: -80px;
			}
		}
		.bottom{
			background-color: $white-cream-transparent;
			z-index: 2;
			border-radius: 30px 30px 0px 0px;
		}
	}

	///////////////////////////
	/////   MediaQueries //////
	///////////////////////////
	@media screen and (max-width: $big-phone) {
		.container-pokemon{
			.center{
				background-size: contain;
				img{
					max-width: 75%;
					margin-left: auto;
					margin-right: auto;
				}
			}
		}
	}

	@media screen and (max-width: $phone) {
		.container-pokemon{
			.back{
				position: fixed;
				top: 0;
				left: 0;
				padding: 20px 15px;
			}
			.top{
				padding: 15px;
				flex-direction: column-reverse;
				.left{
					width: 100%;
					h1{
						margin-bottom: 20px !important;
					}
				}
			}
			.center{
				img{
					margin-bottom: -50px;
				}
			}
		}
	}
</style>
