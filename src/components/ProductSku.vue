<template>
  <template v-if="attr.length > 0">
      <div class="sku" :style="{'border-left':  `4px solid ${color}`}" v-for="(item, index) in attr" :key="index">
          <div class="attr-name">
              <input class="attr-name-value" v-model="item.name" type="text" placeholder="输入规格名">
              <span class="delN" :style="{'color':color}" @click="onDelSku(index)">x</span>
          </div>
          <div class="add-attr">
              <div class="add-attr-value" :style="{'color':color}" v-for="(item2, index2) in item.value" :key="index2">
              <div>{{ item2 }}</div>
              <div class="delv" :style="{'color':color}" @click="onDelValue(index, index2)">x</div>
              </div>
              <div class="attr-value">
              <input class="attr-value-value" v-model="addValues[index]" type="text" placeholder="输入规格值,多个值可用空格隔开一次添加">
              <span class="addIcon" @click="onAddValue(index)">+</span>
              </div>
          </div>
      </div>
  </template>

  <button class="sku-btn" :style="{ 'background-color': color }" @click="onAddSku">添加规格</button>
  <table border="1" v-if="attr.length > 0">
      <thead>
          <tr>
              <template v-for="attribute in attr" :key="attribute.name">
              <th v-if="attribute.name && attribute.value.length > 0">{{ attribute.name }}</th>
              </template>
              <th v-if="props.productPrice">价格</th>
              <th v-if="props.memberPrice">会员价格</th>
              <th v-if="props.productStock">库存</th>
          </tr>
      </thead>
      <tbody>
          <tr v-for="(combination, rowIndex) in combinations" :key="rowIndex">
              <template v-for="(value, attrIndex) in combination">
                  <td
                      v-if="shouldRenderCell(rowIndex, attrIndex)"
                      :rowspan="rowspans[attrIndex]"
                  >
                      {{ value }}
                  </td>
              </template>
              <td v-if="props.productPrice">
                  <input class="attr-name-value" v-model="cartesianProducts[rowIndex].productPrice" type="number" placeholder="价格">
              </td>
              <td v-if="props.memberPrice">
                  <input class="attr-name-value" v-model="cartesianProducts[rowIndex].memberPrice" type="number" placeholder="会员价格">
              </td>
              <td v-if="props.productStock">
                  <input class="attr-name-value" v-model="cartesianProducts[rowIndex].productStock" type="number" placeholder="库存">
              </td>
          </tr>
      </tbody>
  </table>
</template>

<script setup>
  import { ref, watch } from 'vue';
  defineOptions({
    name: 'ProductSku'
  })
  const attr = ref([]);
  const addValues = ref([]);
  const combinations = ref([]);
  const rowspans = ref([]);
  const cartesianProducts = ref([]);

  const props = defineProps({
      color: {
          type: String,
          default: '#1FABC4'
      },
      productStock:{
          type: Boolean,
          default: true
      },
      productPrice: {
          type: Boolean,
          default: true
      },
      memberPrice: {
          type: Boolean,
          default: true
      },
  });
// 定义自定义事件
  const emit = defineEmits(['dataReturned']);

  watch(attr, (newVal, oldVal) => {
      generateTableData(hasEmptyNameOrValue(newVal))
      getCartesianProducts(hasEmptyNameOrValue(newVal))
      // 方法：发送数据到父组件:attr为规格项目,cartesianProducts为属性阵列
      const dataToSend = {
        attr: hasEmptyNameOrValue(newVal),
        cartesianProducts: cartesianProducts.value
      };
      emit('dataReturned', dataToSend);
    },
    { 
      deep: true 
    }
  )
  
  //添加规格
  const onAddSku = () => {
    attr.value.push({
      name: '',
      value: []
    });
  }
  //删除规格
  const onDelSku = (index) => {
    attr.value.splice(index, 1);
  }
  //添加规格值
  const onAddValue = (index) => {
    if (!addValues.value[index] || !addValues.value[index].trim()){
      addValues.value[index] = '';
      return;
    }
    var colorArray = addValues.value[index].trim().split(/\s+/)
    attr.value[index].value = [...new Set([...attr.value[index].value, ...colorArray])]
    addValues.value[index] = '';
  }
  //删除规格值
  const onDelValue = (index,index2) => {
    attr.value[index].value.splice(index2, 1);
  }
  
  const hasEmptyNameOrValue = (array) => {
    return array.filter(item => item.name !== '' && Array.isArray(item.value) && item.value.length > 0);
  }
  // 生成表格数据的函数
  const generateTableData = (attr) => {
    // 获取所有属性值的数组
    const valuesArray = attr.map(attribute => attribute.value);
    // 生成所有组合
    combinations.value = getCombinations(valuesArray);

    // 计算每个属性的 rowspan
    rowspans.value = calculateRowspans(attr);
  };

  // 获取所有可能的组合
  const getCombinations = arrays => {
    return arrays.reduce((acc, curr) => {
      const res = [];
      acc.forEach(a => {
        curr.forEach(c => {
          res.push([...a, c]);
        });
      });
      return res;
    }, [[]]);
  };

  //构建最终的商品数据
  const getCartesianProducts = (attr) => {
    cartesianProducts.value = combinations.value.map(combination => {
      const productSpec = {};
      combination.forEach((value, index) => {
        const attrName = attr[index].name;
        productSpec[attrName] = value;
      });
      return {
        productSpec,
        productPrice: '',
        memberPrice: '',
        productStock: ''
      };
    });
  };
  // 计算每个属性的 rowspan
  const calculateRowspans = attributes => {
    const spans = [];
    const lengths = attributes.map(attr => attr.value.length);
    for (let i = 0; i < lengths.length; i++) {
      let span = 1;
      for (let j = i + 1; j < lengths.length; j++) {
        span *= lengths[j];
      }
      spans.push(span);
    }
    return spans;
  };

  // 判断是否需要渲染单元格
  const shouldRenderCell = (rowIndex, attrIndex) => {
    const span = rowspans.value[attrIndex];
    return rowIndex % span === 0;
  };

</script>

<style>
  .sku-btn{
    background-color: #1FABC4;
    color: #fff;
    border: 0;
    padding: 7px 20px;
    border-radius: 5px;
    cursor: pointer;
  }
  .sku{
    padding-left: 5px;
    border-left: 4px solid #1FABC4;
    margin-bottom: 20px;
  }
  .attr-name{
    background-color: #F8F8F8;
    padding:10px 20px 10px 5px;
    display: flex;
    justify-content: space-between;
  }
  .attr-name-value{
    outline: none;
    border: 1px solid #e5e6e7;
    height: 32px;
    line-height: 32px;
    border-radius: 4px;
    padding: 0 15px;
  }
  .attr-name-value::placeholder {
      color: #a8a4a4;
  }
  .add-attr{
    display: flex;
    align-items: center;
    padding: 0 5px;
    flex-wrap: wrap;
  }
  .add-attr-value{
    margin: 10px 10px 0 0;
    border: 1px solid #e5e6e7;
    height: 32px;
    line-height: 32px;
    padding: 0 15px;
    border-radius: 4px;
    color: #1FABC4;
    display: flex;
  }
  .delv{
    color: #067488;
    cursor: pointer;
    width: 32px;
    text-align: center;
  }
  .delN{
    font-size: 22px;
    color: #1FABC4;
    cursor: pointer;
  }
  .attr-value{
    width: 280px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 32px;
    line-height: 32px;
    border-radius: 4px;
    border: 1px solid #e5e6e7;
    padding: 0 10px;
    margin-top: 10px;
  }
  .attr-value-value{
    outline: none;
    border: none;
    flex: 1;
  }
  .attr-value-value::placeholder {
      color: #a8a4a4;
  }
  .addIcon{
    font-size: 22px;
    color: #C0C4CC;
    cursor: pointer;
  }
  table {
      width: 100%;
      border-collapse: collapse;
      margin: 20px 0; /* 可选：为表格添加一些外部间距 */
  }
  table, th, td {
      border: 1px solid #e9e4e4;
  }
  th, td {
      padding: 10px;
      text-align: center; /* 确保内容水平居中 */
  }
  th {
      background-color: #F8F8F8;
  }
</style>
