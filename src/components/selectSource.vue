<template>
  <div v-for="item in source " class="selectOption" :key="item">
    <div>
    <img  class="itemPreview"
             :id="item + '_item'"
             :src="url +  item + '.png'"
             @click="setItem(item)"/>
      <span class="itemName">{{item}}</span></div>
  </div>
</template>

<script>
export default {
  props:{
    selectId:{
      type: Number
    },
    url:{
      type: String
    },
    nameSelect:{
      type: String
    },
    source:{
      type: Array
    },
  },
  methods:{
 setItem(item){
   this.$emit('selectItem', this.selectId, item)
 }
  },
  name: "selectSource"
}
</script>

<style scoped>
.selectOption{
  display: flex;
}
.itemPreview{
  cursor: pointer;
  height: 100px;
  width: 130px;
}
.itemName{
  position: relative;
  top: -90px;
  left: 10px;
  padding: 1px;
  background: #EDEDED;
}
</style>