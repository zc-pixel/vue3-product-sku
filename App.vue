<template>
  <div id="app">
    <ProductSku :productStock="true" :productPrice="true" :memberPrice="true"  @dataReturned="handleDataReturned" />
  </div>
</template>
<script setup>
import ProductSku from './components/ProductSku.vue';
 
// 方法：处理从子组件返回的数据
const handleDataReturned = (data) => {
  console.log(data.attr);//规格项目
  console.log(data.cartesianProducts);//属性阵列
};
</script>
