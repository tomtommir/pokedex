<template>
  <div>
    <ul class="">
      <li
        v-for="(tab, index) in tabList"
        :key="index"
        class=""
        :class="{
          'text-blue': index + 1 === activeTab,
          'text-white': index + 1 !== activeTab,
        }"
      >
        <label
          :for="`${_uid}${index}`"
          v-text="tab"
          class=""
        />
        <input
          :id="`${_uid}${index}`"
          type="radio"
          :name="`${_uid}-tab`"
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
        class=" "
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