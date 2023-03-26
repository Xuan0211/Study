<template>
  <div class='page'>
    <div class='top'>
      <div class='control'>
        <h1>控制面板</h1>
        <h3>数据集</h3>
        <div class="controllist">
          <el-button @click="click(0)">李白</el-button>
          <el-button @click="click(1)">杜甫</el-button>
          <el-button @click="click(2)">白居易</el-button>
        </div>
        <div class='varcol'>
          <h5>参数A</h5>
          <el-input class='input' placeholder='请输入参数' v-model="myColor" @change="change"></el-input>
        </div>
        <div class='varcol'>
          <h5>参数B</h5>
          <el-input class='input' placeholder='请输入参数' v-model='myPadding' @change="change"></el-input>
        </div>
      </div>
      <div class='view1'>
        <h1>视图1</h1>
        <svg id="chart" width="800" height="600"></svg>
      </div>
      <div class='view2'>
        <h1>视图2</h1>
        <svg id="rightchart" width="100" height="600"></svg>
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
      myColor: 'red',
      myPadding: 0.2,
      path: [
        '"../../data/李白.csv"',
        '../../data/杜甫.csv',
        '../../data/白居易.csv'
      ],
      databank: [
        [
          { word: '天', value: 100 },
          { word: '道', value: 200 },
          { word: '有', value: 300 },
          { word: '轮', value: 150 },
          { word: '回', value: 120 },
          { word: '啊', value: 320 }
        ],
        [
          { word: '地', value: 100 },
          { word: '势', value: 130 },
          { word: '坤', value: 106 },
          { word: '厚', value: 200 },
          { word: '德', value: 300 },
          { word: '载', value: 120 }
        ],
        [
          { word: '人', value: 400 },
          { word: '定', value: 120 },
          { word: '胜', value: 300 },
          { word: '天', value: 200 },
          { word: '人', value: 104 },
          { word: '和', value: 150 }
        ]
      ],
      curIndex: 0,
      dataset: null,
      clicktimes: 0
    };
  },
  methods: {
    generateVis() {
      console.log('D3开始渲染');
      const svg = d3.select('#chart');
      const width = +svg.attr('width');
      const height = +svg.attr('height');
      const margin = { top: 50, bottom: 150, left: 50, right: 100 };
      const innerwidth = width - margin.left - margin.right;
      const innerheight = height - margin.top - margin.bottom;
      // 设置坐标轴
      const xScale = d3.scaleLinear()
        .domain([0, d3.max(this.databank[this.curIndex], d => d.value)])
        .range([0, innerwidth]);
      const yScale = d3.scaleBand()
        .domain(this.databank[this.curIndex]
          .map(d => d.word))
        .range([0, innerheight])
        .padding(this.myPadding);//设置中间间隔
      const g = svg.append('g')
        .attr('id', 'maingroup')
        .attr('transform', `translate(${margin.left},${margin.top})`);
      const yAxis = d3.axisLeft(yScale);
      g.append('g')
        .call(yAxis);
      const xAxis = d3.axisBottom(xScale);
      g.append('g')
        .attr('transform', `translate(${0},${innerheight})`)
        .call(xAxis);
      this.databank[this.curIndex].forEach(d => {
        g.append('rect')
          .attr('width', xScale(d.value))
          .attr('height', yScale.bandwidth())
          .attr('fill', this.myColor )
          .attr('y', yScale(d.word));
      });      
      g.selectAll('rect').on("click",(d,i) =>
      {
          console.log('点击事件触发');
          console.log(d,i);
          this.clicktimes ++;
          const svg = d3.select("#rightchart");
          const width= +svg.attr('width');
          const height= +svg.attr('height');
          svg.append('g')
          .append('text')
          .attr('transform',`translate(0,${this.clicktimes*50})`)
          .text(`点击事件${this.clicktimes}`);
        });
    },
    click(e) {
      this.curIndex = e;
      console.log(e);
      // 想要动画捏
      d3.select('#maingroup').remove();      
      this.generateVis();
    //  d3.select(`#data${e}`).attr('width',200);
    //这句为啥不生效，一定要是svg吗？
    },
    change()
    {
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

.controllist {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
</style>
