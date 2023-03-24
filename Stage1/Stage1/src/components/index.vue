<template>
  <div class='page'>
    <div class='top'>
      <div class='control'>
        <h1>控制面板</h1>
        <h3>数据集</h3>
        <h4 @click="click(0)">李白</h4>
        <h4 @click="click(1)">杜甫</h4>
        <h4 @click="click(2)">白居易</h4>
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
        <svg id="chart" width="800" height="600"></svg>
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

export default {
  name: 'index',
  data() {
    return {
      var1: '',
      var2: '',
      path: [
        '"../../data/李白.csv"',
        '../../data/杜甫.csv',
        '../../data/白居易.csv'
      ],
      databank: [
        [
          {word: '天', value: 100},
          {word: '道', value: 200},
          {word: '有', value: 300},
          {word: '轮', value: 150},
          {word: '回', value: 120},
          {word: '啊', value: 320}
        ],
        [
          {word: '地', value: 100},
          {word: '势', value: 130},
          {word: '坤', value: 106},
          {word: '厚', value: 200},
          {word: '德', value: 300},
          {word: '载', value: 120}
        ],
        [
          {word: '人', value: 400},
          {word: '定', value: 120},
          {word: '胜', value: 300},
          {word: '天', value: 200},
          {word: '人', value: 104},
          {word: '和', value: 150}
        ]
      ],
      curIndex: 0,
      dataset: null
    };
  },
  methods: {
    generateVis() {
      console.log('D3开始渲染');
      const svg = d3.select('#chart');
      const width = +svg.attr('width');
      const height = +svg.attr('height');
      const margin = {top: 60, bottom: 60, left: 90, right: 10};
      const innerwidth = width - margin.left - margin.right;
      const innerheight = height - margin.top - margin.bottom;
      // 设置坐标轴
      const xScale = d3.scaleLinear().domain([0, d3.max(this.databank[this.curIndex], d => d.value)]).range([0, innerwidth]);
      const yScale = d3.scaleBand().domain(this.databank[this.curIndex].map(d => d.word)).range([0, innerheight]);
      const g = svg.append('g').attr('id', 'maingroup').attr('transform', 'translate(80,50)');
      // todo 问题出在这里了
      const yAxis = d3.axisLeft(yScale);
      g.append('g').call(yAxis);
      const xAxis = d3.axisBottom(xScale);
      g.append('g').call(xAxis);
      this.databank[this.curIndex].forEach(d => {
        g.append('rect').attr('width', xScale(d.value)).attr('height', yScale.bandwidth()).attr('fill', 'green').attr('y', yScale(d.word));
      });
    },
    click(e) {
      this.curIndex = e;
      console.log(e);
      d3.select('#maingroup').remove();
      this.generateVis();
    }
  },
  mounted() {
    window.vue = this;// 试图用console调用vue内部的函数 \
    this.dataset = this.databank[0];
    console.log(this.dataset);
    this.generateVis();
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
