![Vue3 Product SKU Demo](https://github.com/zc-pixel/vue3-product-sku/raw/main/images/demo.png)

```markdown


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
  <div id="app">
      <Vue3ProductSku color="black" :productStock="true" :productPrice="true" :memberPrice="true"  @dataReturned="handleDataReturned" />
</div>
</template>
<script setup>
import Vue3ProductSku from 'vue3-product-sku';
 
// 方法：处理从子组件返回的数据
const handleDataReturned = (data) => {
  console.log(data.attr);//规格项目
  console.log(data.cartesianProducts);//属性阵列
};
</script>
```

## 配置选项
`vue3-product-sku` 插件接受以下配置选项：
 
| 参数名         | 类型       | 必填  | 描述                |
|---------------|------------|------|---------------------|
| `color`       | String     | 否   | 插件主题颜色。        |
| `productStock`| Boolean    | 否   | 规格的库存。          |
| `productPrice`| Boolean    | 否   | 规格的价格。          |
| `memberPrice` | Boolean    | 否   | 规格的会员价、折扣价。 |
 
## 事件

- `@dataReturned`: 返回规格项目和属性阵列。

## 开发

如果你打算贡献代码或开发这个插件，你可以使用以下命令：

```bash
# 启动开发服务器
npm run dev

# 构建插件
npm run build

# 预览构建结果
npm run preview
```

## 作者

zhangchao <https://15191413613.github.io/resume>

## 许可证

这个插件在 MIT 许可证下发布。你可以自由地使用、修改和分发它，但请遵守许可证中的条款。
