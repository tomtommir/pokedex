<template>
    <ul>
        <li 
            v-for="stat in stats" 
            :key="stat.stat.name" 
            :class="stat.stat.name">
            <span class="title">{{ stat.stat.name }}</span>
            <span class="content">{{ stat.base_stat }}</span>
            <span class="line-container"> 
                <span class="line" :style="{ width:(stat.base_stat/320) * 100 + '%' }"></span>
                <span class="full-line"></span>
            </span>
        </li>
        <li class="total">
            <span class="title">Total</span>
            <span class="content">{{ total }}</span>
            <span class="line-container"> 
                <span class="line" :style="{ width:(total/720) * 100 + '%' }"></span>
                <span class="full-line"></span>
            </span>
        </li>
    </ul>
</template>

<script>
import axios from "axios"

export default {
	name: "StatsContent",
    props: ["idPokemon"],
    watch: {
        idPokemon: function(idPokemon) {
			this.getStats(idPokemon)
        }
	},
	data() {
		return {
			stats: [],
            total: 0,
		}
	},
	methods: {
        getStats(id){
            const self = this

            axios.get("https://pokeapi.co/api/v2/pokemon/"+id)
            .then(response => {
                this.stats = response.data.stats
                //On récupère la stat total
                this.stats.forEach(function(item){
                    self.total = item.base_stat + self.total
                })
            })
            .catch(() => { 
                this.error = true
            })
            .finally(() => (this.loading = false))
        },
	},
    mounted() {
        this.getStats(this.idPokemon)
    }
}
</script>

<style lang="scss" scoped>
    ul{
        margin: 0;
        padding: 30px 0 0 0;
        list-style: none;
        li{
            display: flex;
            column-gap: 20px;
            padding: 0 0 15px 0;
            .title{
                min-width: 150px;
                opacity: 0.6;
                text-transform: capitalize;
                font-size: 15px;
            } 
            .content{
                font-weight: 500;
                display: flex;
                justify-content: center;
                column-gap: 15px;
                min-width: 40px;
                font-size: 17px;
                i{
                    &.ri-women-line{
                        color: $women;
                    }
                    &.ri-men-line{
                        color: $men;
                    }
                    &.ri-genderless-line{
                        color: $genderless;
                    }
                }
            } 
            .line-container{
                flex-grow: 1;
                display: flex;
                align-items: center;
                .line{
                    display: block;
                    height: 4px;
                }
                .full-line{
                    display: block;
                    height: 3px;
                    background-color: $light-grey;
                    width: 100%;
                }
            }
            &.hp{
                .title{
                    text-transform: uppercase;
                }
            }
            &.hp,&.defense,&.special-defense,&.speed{
                .line-container{
                    .line{
                        background-color: $green;
                    }
                }
            }
            &.attack,&.special-attack,&.total{
                .line-container{
                    .line{
                        background-color: $red;
                    }
                }
            }
        }
    }
    
	///////////////////////////
	/////   MediaQueries //////
	///////////////////////////
	@media screen and (max-width: $tablet) {
        ul{
            li{
                flex-direction: column;
                row-gap: 10px;
                padding: 0 0 30px 0;
                .title{
                    font-size: 14px;
                } 
                .content{
                    position: absolute;
                    left: 200px;
                    font-size: 15px;
                }
            }
        }
    }

    @media screen and (max-width: $phone) {
        ul{
            li{
                .title{
                    
                } 
                .content{
                    position: absolute;
                    left: initial;
                    right: 0;
                }
            }
        }
    }
</style>