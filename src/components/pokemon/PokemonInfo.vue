<template>
	<section 
		id="pokemon-info"
		class="pokemon-item" 
		:class="{active: isActive}"
		>
		<div v-if="loading" class="loading" >
            <img src="/images/pokeball.svg" class='loader'>
        </div>
        <div v-else>
			<div v-if="error" class="error">
				Problem while searching, please search for another pokemon
			</div>
			<div v-else>
				<i 
					class="ri-close-line close-info" 
					@click="close()"></i>
				<div class="pokemon-img">
					<img class="pokemon-item__image" :src="img" :alt="name" />
				</div>
				<h2 class="title">
						{{ name.toLowerCase() }}
				</h2>
				<div class="column_container">
					<div class="column">
						<h3>Abilities</h3>
						<ul class="abilities">
							<li v-for="ability in abilities" :key="ability.name">{{ ability.ability.name }}</li>
						</ul>
					</div>
					<div class="column">
						<h3>Stats</h3>
						<ul class="stats">
							<li v-for="stat in stats" :key="stat.name">{{ stat.stat.name }}: {{ stat.base_stat }}</li>
						</ul>
					</div>
					<div class="column">
						<h3>Moves</h3>
						<ul class="moves">
							<li v-for="move in moves" :key="move.name">{{ move.move.name }}</li>
						</ul>
					</div>
				</div>
			</div>
        </div>
	</section>
</template>

<script>
import axios from "axios"

export default {
	name: "PokemonInfo",
	props: ["name", "url", "clicked"],
	watch: {
		url: function(newUrl) {
			//Si l'on vient mettre une nouvelle URL alors
			//on reload la pokemon Info
			this.getPokemonInfo(newUrl.toLowerCase())
        },
		clicked: function() {
			//Si l'on vient mettre à true la variable clicked
			//on affiche la pokemon info
			this.open()
        }
	},
	data() {
		return {
			abilities:[],
			stats:[],
			moves:[],
			img: "",
			loading: false,
			error: false,
			isActive: true
		}
	},
	methods: {
		getPokemonInfo(url){
			this.loading = true
			this.error = false
			setTimeout(()=>{
				axios.get(url)
				.then(response => {
					this.abilities = response.data.abilities
					this.stats = response.data.stats
					this.moves = response.data.moves
					this.img = response.data.sprites.other.dream_world.front_default
				})
				.catch(() => { 
					this.error = true
				})
				.finally(() => (this.loading = false))
			},500);
		},
		close(){
			this.isActive = false
		},
		open(){
			this.isActive = true
		}
	},
	mounted () {
		this.getPokemonInfo(this.url)
	}

}
</script>

<style scoped lang="scss">
	.pokemon-item{
		position: relative;
        width: 100%;
		padding: 50px;
		background: $white;
		border-radius: 20px;
		flex-direction: column;
		row-gap: 20px;
		display: none;
		&.active{
			display: flex;
		}
		.close-info{
			position: absolute;
			top: 10px;
			right: 20px;
			font-size: 25px;
			cursor: pointer;
		}
		.pokemon-img{
			img{
				width: 200px;
			}
			margin-bottom: 20px;
		}
		h2{
			font-size: 30px;
			font-weight: 500;
			color: $black;
			margin: 0;
			text-transform: capitalize;
			margin-bottom: 20px;
		}
		.column_container{
			display: flex;
			flex-direction: row;
			column-gap: 50px;
			.column{
				width: calc(100% / 3);
				h3{
					font-size: 25px;
					font-weight: 500;
					text-align: left;
				}
				ul{
					margin: 0;
					padding-left: 16px;
					li{
						text-align: left;
						margin: 0;
						text-transform: capitalize;
						font-size: 16px;
						color: grey;
					}
				}
				
			}
		}
	}

	///////////////////////////
	/////   MediaQueries //////
	///////////////////////////
	@media screen and (max-width: $tablet) {
		.pokemon-item{
			max-width: 100%;
			margin: 20px 0;
			padding: 30px;
			.column_container{
				display: flex;
				flex-direction: column;
				column-gap: 20px;
				.column{
					width: 100%;
					ul{
						display: grid;
						grid-template-columns: repeat(3,1fr);
						column-gap: 20px;
					}
					
				}
			}
		}
	}

	@media screen and (max-width: $phone) {
		.pokemon-item{
			padding: 15px;
			.column_container{
				.column{
					ul{
						grid-template-columns: repeat(2,1fr);
					}
					
				}
			}
		}
	}
</style>
