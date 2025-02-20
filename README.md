## 快速体验

[点击体验]: https://play.vuejs.org/#eNqNVE1v00AQ/SurvaSVUucApzSpWmgP4QCBllPdg2tPghvbu6zXaaQoEgcQlI+KK1Ck9oAEJzgCEvyaOunPYHYdO+vKRL1Y9nvPM2/f7O6YbnFuDROgTWpHLQkhDxwJG3ZESMvzh8T32jZ1OLepxhDtCuYlrtwdJCRDCGlyR0Akt6QU16A95zCAbUc6C3z+u2TugLSJTaVIwMbmZb4rfBeq+BDCQxD/pee/d8J+FetBABKQxEWVqU0PTa49TZjMyrJIuX6ovg1RwgPmeGv+vDqLHmsAC9q0SGMz62KotvO2ealGFnADE8a3VsPIvRW7wueSxCATrmV+yJmQZEwE9CakJ1hIajix2rpBGkPJBFZjAan5KrXSuyyKJZH5WNAgVl3ZP1jV1TLWwTlWEosxL6GLkZc0C5WRLCpWVO6rpL1Bxlk0hTVr6ASJkiiFVcC6H9EeywqFzEnViAVgBayf1df4BB+mjWJ2ykbPD6Au2HEn8mBk+sFhNa6b2s+FB1Zpv13+OZ99fJ5++js7eZme/Ug/P6tsXGwH1biq58071mrr6avz6ekJi+Pp2bv09UX64Wvh4fLXm/T96fTni9m331ffv1xdvDW9za2p/ZftOdxttE5ljD57ft86ilmE14L2ZFOXhRwzEg+49HEdNm3mbvF+CAJ2fE9j6lTVc9x9Au6gAj+KRwqzaVdADGKIR6zgpCP6IDN6Z/c+jPC9IENceoDqJeQjwNEnymMmu5NEHto2dNptR58bP+rvxTsjCVGcL0pfCyoZrbcpnp27S5a+sHvLuj1PdIIplk8fxmjerUsPvDmNyT9pjfJX

想要快速体验，直接将ProductSku.vue文件复制进演练场体验  [点击体验]。


# vue3-product-sku

## 简介

`vue3-product-sku` 是一个为 Vue 3 设计的商品 SKU 选择器插件。它提供了灵活且易于使用的界面组件，帮助开发者在 Vue 应用中快速集成商品规格选择功能。

## 安装

你可以通过 npm 或 yarn 来安装这个插件：

```bash
# 使用 npm
npm install vue3-product-sku

# 或者使用 yarn
yarn add vue3-product-sku
```

## 使用

在你的 Vue 组件中引入并使用 `vue3-product-sku` 插件：

```javascript
<template>
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
</template>
<script setup>
  import ProductSku from 'vue3-product-sku';
  const attr = ref([]);
  const tableData = ref([]);
  /**
   * 引用插件数据
   * @param {Object} data - 包含子组件数据的对象
   * @param {Array} data.tableData - 属性阵列，包含表格的数据行
   * @param {Array} data.attr - 规格项目，包含产品的规格信息
   */
  const onDataQuote = (data) => {
    attr.value = data.attr;
    tableData.value = data.tableData;
  }
  /**
   * 上传图片
   * @param file file对象
   * @param rowIndex 行索引 
   * productImg = "true"时有效。
   * 将file对象上传至你的云OSS服务器，然后将返回的图片地址赋值给tableData.value[rowIndex].productImg。
   * 必须先走完onDataQuote方法，完成tableData引用。
   */
  const onUploadImg = (file,rowIndex) => {
    // 将file对象上传至你的云OSS服务器
    // tableData.value[rowIndex].productImg = 你的图片地址;
  }
  /**
   * 删除图片
   * @param rowIndex 行索引
   * deleteImg = "true"时有效。 
   * 不需要删除oss服务器文件时，不需要该方法。插件会清空该行图片地址。
   * 你可以取出tableData.value[rowIndex].productImg的图片地址，删除oss服务器文件之后，再清空该行图片地址tableData.value[rowIndex].productImg = ''。
   * 必须先走完onDataQuote方法，完成tableData引用。
   */
  const onDeleteImg = (rowIndex) => {
    // 删掉oss服务器的图片之后清空该行图片地址
    // tableData.value[rowIndex].productImg = '';
  }
</script>
```

## 配置选项
`vue3-product-sku` 插件接受以下配置选项：
 
|        参数名         |     类型        | 必填  |           描述                         |    默认值     |
|----------------------|-----------------|-------|---------------------------------------|--------------|
| `color`              |      String     |  否   |     插件主题颜色。                      |   #1FABC4   |
| `productStock`       |      Boolean    |  否   |     库存。                             |    true      |
| `productPrice`       |      Boolean    |  否   |     价格。                             |    true      |
| `memberPrice`        |      Boolean    |  否   |     会员价、折扣价。                    |    true      |
| `productImg`         |      Boolean    |  否   |     图片。                             |    true      |
| `deleteImg`          |      Boolean    |  否   |     是否删除OSS图片。                   |    false     |
| `parentAttr`         |      Array      |  否   |     编辑时取出存入数据库的规格。         |    []        |
| `parentTableData`    |      Array      |  否   |     编辑时取出存入数据库的属性阵列。      |    []        |
## 事件
* @data-quote
    > 引用插件数据。    
    > -参数-    
    > data - 包含数据的对象  {Object} 。    
    > data.attr - 规格项目  {Array} 。    
    > data.tableData - 属性阵列  {Array} 。      
    ```javascript
    data.attr规格项目示例：
      [
        {
          "name": "颜色",
          "value": [
              "黑色"
          ]
        },
        {
          "name": "尺码",
          "value": [
              "S"
          ]
        }
      ]
    data.tableData属性阵列示例：
      [
        {
          "productSpec": {
            "颜色": "黑色",
            "尺码": "S"
          },
          "productPrice": "100",
          "memberPrice": "80",
          "productStock": "1000",
          "productImg": "",
          "specs": [
            {"value": "黑色","show": true,"rowspan": 1},
            {"value": "S","show": true,"rowspan": 1}
          ]
        }
      ]
    ```
* @upload-img
    > 上传图片。  
    > -参数-     
    > file - file对象  {file} 。    
    > rowIndex - 行索引  {Number} 。  
    > * productImg = "true"时有效。    
    > * 将file对象上传至你的云OSS服务器，然后将返回的图片地址赋值给tableData.value[rowIndex].productImg。  
    > * 必须先使用@data-quote方法，完成tableData引用。  

* @delete-img
    > 删除图片。  
    > -参数-     
    > rowIndex - 行索引  {Number} 。  
    > * deleteImg = "true"时有效。    
    > * 不需要删除oss服务器文件时，不需要该方法。点击删除插件会清空该行图片地址。    
    > * 你可以取出tableData.value[rowIndex].productImg的图片地址删除oss服务器文件之后，再清空该行图片地址tableData.value[rowIndex].productImg = ''。    
    > * 必须先使用@data-quote方法，完成tableData引用。         

## 作者

zhangchao <https://15191413613.github.io/resume>

## 许可证

这个插件在 MIT 许可证下发布。你可以自由地使用、修改和分发它，但请遵守许可证中的条款。
