<template>
  <div>
    <ul class="tab-nav">
      <li
        v-for="(tab, index) in tabList"
        :key="index"
        class=""
        :class="{
          'tab-active': index + 1 === activeTab,
          'tab-inactive': index + 1 !== activeTab,
        }"
      >
        <label
          :for="`input-${index}`"
          v-text="tab"
          class=""
        />
        <input
          :id="`input-${index}`"
          type="radio"
          :name="`input-${index}-tab`"
          :value="index + 1"
          v-model="activeTab"
          class="hidden"
        />
      </li>
    </ul>
    <template v-for="(tab, index) in tabList">
      <div
        :key="index"
        v-if="index + 1 === activeTab"
        class="tab-content"
      >
        <slot :name="`tabPanel-${index + 1}`" />
      </div>
    </template>
  </div>
</template>

<script>
export default {
  props: {
    tabList: {
      type: Array,
      required: true,
    },
    variant: {
      type: String,
      required: false
    },
  },
  data() {
    return {
      activeTab: 1,
    };
  },
};
</script>

<style lang="scss" scoped>
	ul.tab-nav{
		list-style: none;
		display: flex;
		justify-content: space-between;
		padding: 0;
		margin: 0;
		max-width: 50%;
    	margin: 0 auto;
		li{
			label{
				display: flex;
				padding: 10px;
				font-size: 25px;
				color: $black;
				transition: $transition;
			}
			&.tab-active{
				border-bottom: 2px solid;
			}
			&.tab-inactive{
				label{
					opacity: 0.5;
				}
			}
			.hidden{
				display: none;
			}
		}
	}

	///////////////////////////
	/////   MediaQueries //////
	///////////////////////////
	@media screen and (max-width: $big-phone) {
		ul.tab-nav{
			max-width: 100%;
			li{
				label{
					font-size: 20px;
				}
			}
		}
	}

	@media screen and (max-width: $phone) {
		ul.tab-nav{
			li{
				label{
					font-size: 16px;
				}
			}
		}
	}
</style>