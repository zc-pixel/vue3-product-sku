
<template>
  <div id="app">
    <ProductSku 
      :parentAttr
      :parentTableData
      :productStock = "true"
      :productPrice = "true"
      :memberPrice = "true"
      :productImg = "true"
      :deleteImg="true"
      @data-quote = "onDataQuote"
      @upload-img = "onUploadImg" 
      @delete-img = "onDeleteImg"
    />
  </div>
</template>
<script setup>
  import { ref} from 'vue';
  import ProductSku from './components/ProductSku.vue';

  const tableData = ref([]);
  const attr = ref([]);

  const parentAttr = ref([]);
  const parentTableData = ref([]);
  /**
   * 引用插件数据
   * @param {Object} data - 包含子组件数据的对象
   * @param {Array} data.tableData - 属性阵列，包含表格的数据行
   * @param {Array} data.attr - 规格项目，包含产品的规格信息
   */
  const onDataQuote = (data) => {
    tableData.value = data.tableData;
    attr.value = data.attr;
    console.log(data);
  }
  /**
   * 上传图片
   * @param file file对象
   * @param rowIndex 行索引 
   * productImg = "true"时有效。
   * 规格不需要图片时，不需要实现该方法。
   * 将file对象上传至你的云OSS服务器，然后将返回的图片地址赋值给tableData.value[rowIndex].productImg。
   * 必须先走完onDataQuote方法，完成tableData引用。
   */
  const onUploadImg = (file,rowIndex) => {
      // tableData.value[rowIndex].productImg = 你的图片地址;
  }
  /**
   * 删除图片
   * @param rowIndex 行索引
   * deleteImg="true"时有效。
   * 不需要删除oss服务器文件时，不需要实现该方法。插件会自动清空该行图片地址。
   * 你可以取出tableData.value[rowIndex].productImg的图片地址删除oss服务器文件之后，再清空该行图片地址tableData.value[rowIndex].productImg = ''。
   * 必须先走完onDataQuote方法，完成tableData引用。
   */
  const onDeleteImg = (rowIndex) => {
    // tableData.value[rowIndex].productImg = '';删掉oss服务器的图片之后清空该行图片地址
  }

</script>
