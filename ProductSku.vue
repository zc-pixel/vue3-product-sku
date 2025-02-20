<template>
  <template v-if="attr.length > 0">
      <div class="sku" :style="{'border-left':  `4px solid ${color}`}" v-for="(item, index) in attr" :key="index">
          <div class="attr-name">
              <input class="attr-name-value" v-model.trim="item.name" type="text" placeholder="输入规格名">
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
           <th v-for="header in headers" :key="header">{{ header }}</th>
           <th v-if="productPrice">价格</th>
           <th v-if="memberPrice">会员价格</th>
           <th v-if="productStock">库存</th>
           <th v-if="productImg">产品图片</th>
        </tr>
     </thead>
     <tbody>
        <tr v-for="(row, rowIndex) in tableData" :key="rowIndex">
           <template v-for="(cell, cellIndex) in row.specs" :key="cellIndex">
              <td v-if="cell.show" :rowspan="cell.rowspan">{{ cell.value }}</td>
           </template>
           <td v-if="productPrice">
              <input class="attr-name-value" v-model="row.productPrice" type="number" placeholder="价格">
            </td>
            <td v-if="memberPrice">
              <input class="attr-name-value" v-model="row.memberPrice" type="number" placeholder="会员价格">
            </td>
            <td v-if="productStock">
              <input class="attr-name-value" v-model="row.productStock" type="number" placeholder="库存">
            </td>
            <td v-if="productImg" style="width: 20%;">
              <div class="productImg">
                <template v-if="row.productImg">
                  <img :src="row.productImg" alt="点击放大图片" height="50" @click="showModal(rowIndex)" />
                  <button @click="deleteFile(rowIndex)" class="delete-button">❌</button>
                </template>
                <template v-else>
                  <input type="file" accept="image/*" id="upload" @change="handleFileChange($event,rowIndex)" />
                </template>
              </div>
            </td>
        </tr>
     </tbody>
  </table>

  <div v-if="isModalVisible" @click="hideModal" class="modal-overlay">
    <div class="modal">
      <button @click="hideModal" class="close-button">&times;</button>
      <img :src="selectedImage" alt="放大图片" class="modal-image" />
    </div>
  </div>

</template>

<script setup>
  import { ref, watch,onMounted } from 'vue';
  const attr = ref([]);
  const addValues = ref([]);
  const isModalVisible = ref(false);
  const selectedImage = ref('');
  const tableData = ref([]);
  const headers = ref([]);

  const props = defineProps({
    color: {
      type: String,
      default: '#1FABC4'
    },
    productStock: {
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
    productImg: {
      type: Boolean,
      default: true
    },
    deleteImg: {
      type: Boolean,
      default: false
    },
    parentTableData: {
      type: Array,
      default: () => []
    },
    parentAttr: {
      type: Array,
      default: () => []
    }
  });
  //自定义emits事件
  const emit = defineEmits(['data-quote', 'upload-img', 'delete-img']);
  // 编辑时初始化数据
  onMounted(() => {
    if (props.parentTableData.length > 0 && props.parentAttr.length > 0) {
      console.log(1111111111);
      headers.value = props.parentAttr.map(item => item.name);
      attr.value = [...props.parentAttr];
      tableData.value = [...props.parentTableData];
    }
  });
  // 监听规格变化并通过规格生成表格数据同时将数据通过data-quote返回给父组件
  watch(() => attr.value, (newVal) => {
    generateTableData(hasEmptyNameOrValue(newVal));
  }, { deep: true });
  // 添加规格
  const onAddSku = () => {
    attr.value.push({
      name: '',
      value: []
    });
  };
  // 删除规格
  const onDelSku = (index) => {
    attr.value.splice(index, 1);
  };
  // 添加规格值
  const onAddValue = (index) => {
    if (!addValues.value[index] || !addValues.value[index].trim()) {
      addValues.value[index] = '';
      return;
    }
    const colorArray = addValues.value[index].trim().split(/\s+/);
    attr.value[index].value = [...new Set([...attr.value[index].value, ...colorArray])];
    addValues.value[index] = '';
  };
  // 删除规格值
  const onDelValue = (index, index2) => {
    attr.value[index].value.splice(index2, 1);
  };
  // 上传图片
  const handleFileChange = (event, rowIndex) => {
    const file = event.target.files[0];
    if (file && file.type.startsWith('image/')) {
      emit('upload-img', file, rowIndex);
    } else {
      alert('Please select a valid image file.');
    }
  };
  // 删除图片
  const deleteFile = (index) => {
    if(props.deleteImg){
      emit('delete-img', index);
    }else{
      tableData.value[index].productImg = ''; 
    }
  };
  // 显示放大图片
  const showModal = (index) => {
    isModalVisible.value = true;
    selectedImage.value = tableData.value[index].productImg;
  };
  // 放大图片隐藏
  const hideModal = () => {
    isModalVisible.value = false;
  };
  // 生成商品及表格数据
  const generateTableData = (data) => {
    headers.value = data.map(item => item.name);
    const transformedData = transformData(data, tableData.value);
    tableData.value = prepareTableData(transformedData, headers.value);
    dataToSend();
  };
  // 父组件接收数据
  const dataToSend = () => {
    const dataReturned = {
      attr: attr.value,
      tableData: tableData.value
    };
    emit('data-quote', dataReturned);
  };
  //数据转换并存储已输入价格、库存等数据的行
  const transformData = (data, existingData) => {
    const keys = data.map(item => item.name);
    const values = data.map(item => item.value);
    const combinations = cartesianProduct(...values);

    const existingDataMap = new Map();
    existingData.forEach(item => {
      const key = keys.map(k => item.productSpec[k]).join('|');
      existingDataMap.set(key, item);
    });

    return combinations.map(combo => {
      const productSpec = {};
      keys.forEach((key, index) => {
        productSpec[key] = combo[index];
      });
      const key = keys.map(k => productSpec[k]).join('|');
      return existingDataMap.get(key) || {
        productSpec,
        productPrice: "",
        memberPrice: "",
        productStock: "",
        productImg: ""
      };
    });
  };
  const prepareTableData = (data, headers) => {
    const rowspanData = headers.reduce((acc, header) => {
      acc[header] = calculateRowspan(data, header);
      return acc;
    }, {});
    return data.map((item, rowIndex) => {
      const specs = headers.map(header => ({
        value: item.productSpec[header],
        show: rowspanData[header][rowIndex]?.show ?? true,
        rowspan: rowspanData[header][rowIndex]?.rowspan ?? 1
      }));
      return {
        ...item,
        specs
      };
    });
  };
  const cartesianProduct = (...arrays) => {
    return arrays.reduce((acc, curr) => {
      return acc.flatMap(d => curr.map(e => [d, e].flat()));
    }, [[]]);
  };
  const calculateRowspan = (data, header) => {
    const result = [];
    let lastValue = null;
    let lastIndex = 0;
    data.forEach((item, index) => {
      if (item.productSpec[header] !== lastValue) {
        if (lastValue !== null) {
          const count = index - lastIndex;
          for (let i = lastIndex; i < index; i++) {
            result[i] = { show: i === lastIndex, rowspan: count };
          }
        }
        lastValue = item.productSpec[header];
        lastIndex = index;
      }
    });
    const count = data.length - lastIndex;
    for (let i = lastIndex; i < data.length; i++) {
      result[i] = { show: i === lastIndex, rowspan: count };
    }
    return result;
  };
  const hasEmptyNameOrValue = (array) => {
    return array.filter(item => item.name !== '' && Array.isArray(item.value) && item.value.length > 0);
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
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.8);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
  }
  .modal {
    position: relative;
    background: white;
    padding: 20px;
    border-radius: 8px;
    max-width: 90%;
    max-height: 90%;
    overflow: auto;
  }

  .close-button {
    position: absolute;
    top: 0px;
    right:0px;
    font-size: 30px;
    cursor: pointer;
    background: none; 
    border: none;
  }

  .modal-image {
    width: 500px;
    height: auto;
  }
  .productImg{
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding-right: 10%;
  }
  .productImg img{
    border: 1px solid #e5e6e7;
    border-radius: 5px;
  }
  .delete-button{
    font-size: 18px;
    cursor: pointer;
    background: none; 
    border: none;
    margin-left: 20px;
  }
</style>

