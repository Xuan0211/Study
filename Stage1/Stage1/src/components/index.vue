<template>
  <div class='page'>
    <div class='top'>
      <div class='control'>
        <h1>控制面板</h1>
        <h3>数据集</h3>
        <el-dropdown class='dropdown'>
          <span class='dropdowntext'>
            选择数据集
          </span>
          <template #dropdown>
            <el-dropdown-menu>
              <el-dropdown-item>李白</el-dropdown-item>
              <el-dropdown-item>杜甫</el-dropdown-item>
              <el-dropdown-item>白居易</el-dropdown-item>
            </el-dropdown-menu>
          </template>
        </el-dropdown>
        <el-divider></el-divider>
        <div class='varcol'>
          <h5>参数A</h5>
          <el-input class='input' v-model='var1' placeholder='请输入参数'></el-input>
        </div>
        <div class='varcol'>
          <h5>参数B</h5>
          <el-input class='input' v-model='var2' placeholder='请输入参数'></el-input>
        </div>
      </div>
      <div class='view1'>
        <h1>视图1</h1>
        <svg width="600" height="500"></svg>
      </div>
      <div class='view2'>
        <h1>视图2</h1>
      </div>
    </div>
    <div class='buttom'>
      <h1>视图3</h1>
    </div>
  </div>
</template>
<script>
import * as d3 from 'd3';
import { config } from 'vue/types/umd';

export default {
  name: 'index',
  data() {
    return {
      var1: '',
      var2: '',
      path: [
        '../../data/李白.csv',
        '../../data/杜甫.csv',
        '../../data/白居易.csv'
      ],
      dataset: null
    };
  },
  methods: {
    generateVis() {
      config.log('hello');
    }
  },
  mounted() {
    d3.csv('path[0]', function (error, data) {
      if (error) { // 如果 error 不是 null，肯定出错了
        console.log(error); // 输出错误消息
      } else { // 如果没出错，说明加载文件成功了
        console.log(data); // 输出数据
        // 包含成功加载数据后要执行的代码
        this.dataset = data;
        this.generateVis();
      }
    });
  }
};
</script>
<style>
.page {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.top {
  height: 75%;
  display: flex;
  flex-direction: row;
}

.buttom {
  height: 25%;
}

.control {
  width: 20%;
  display: flex;
  flex-direction: column;
  gap: 5%;
  padding-right: 30px;
}

.view1 {
  width: 50%;
}

.vew2 {
  width: 30%;
}

.dropdown {
  border: 0.1em solid black;
  padding: 10px 10px;
}

.dropdowntext {
  width: auto;
}

.example-showcase .el-dropdown-link {
  cursor: pointer;
  color: var(--el-color-primary);
  display: flex;
  align-items: center;
}

.varcol {
  display: flex;
  flex-direction: row;
  align-content: center;
}

.input {
  width: auto;
}
</style>
