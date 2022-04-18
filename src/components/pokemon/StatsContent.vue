<template>
    <ul>
        <li 
            v-for="stat in stats" 
            :key="stat.stat.name" >
            <span class="title">{{ stat.stat.name }}</span>
            <span class="content">{{ stat.base_stat }}</span>
            <span class="line-container"> 
                <span class="line" :style="{ width:(stat.base_stat/320) * 100 + '%' }"></span>
            </span>
        </li>
        <li>
            <span class="title">Total</span>
            <span class="content">{{ total }}</span>
            <span class="line-container"> 
                <span class="line" :style="{ width:(total/720) * 100 + '%' }"></span>
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