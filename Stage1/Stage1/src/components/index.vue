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
        <svg id="rightchart" width="100" height="300"></svg>
        <div>
        <el-button @click="startselection">开始框选</el-button>
        <el-button @click="endselection">结束框选</el-button>
      </div>
        <p>框选元素</p>
        <div v-for="item in selection">
          {{ item.word }}{{ item.value }}
        </div>
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
      clicktimes: 0,
      selection:undefined,
      lastselection:'',
      linedata:[],
    };
  },
  methods: {
    generateVis() {
      let that= this;
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
      // 绘制矩形图
      g.selectAll('rect').data(this.databank[this.curIndex]).join('rect')
        .transition().duration(100)
        .attr('width', d => xScale(d.value))
        .attr('height', yScale.bandwidth())
        .attr('fill', this.myColor)
        .attr('y', d => yScale(d.word));
        g.selectAll("circle")
        .data(this.databank[this.curIndex])
        .enter()
        .append("circle")
        .attr("cx", function(d) {
              return xScale(d.value);
        })
        .attr("cy", function(d) {
              return yScale(d.word)+0.5*yScale.bandwidth();
        })
        .attr("r", 5)
        .style("fill","black")
        .on("click",(d,i) =>
        {
          console.log('点击事件触发');
          console.log(d, i);
          that.clicktimes++;
          const svg = d3.select("#rightchart");
          const width = +svg.attr('width');
          const height = +svg.attr('height');
          svg.append('g')
            .append('text')
            .attr('transform', `translate(0,${that.clicktimes * 50})`)
            .text(`点击事件${that.clicktimes}`);
          that.linedata.push({
            word: i.word,
            value: i.value
          });
          
          console.log(that.linedata);
          let line = d3.line()
                    .x(function(d)
                    {
                      return xScale(d.value);
                    })
                    .y(function(d)
                    {
                      return yScale(d.word)+0.5*yScale.bandwidth();
                    });
          g.append('path')
              .attr('class', 'line')
              .attr('d', line(that.linedata))
              .attr('fill', 'none')
              .attr('stroke-width', 3)
              .attr('stroke', 'green');;
        });
    
    },
    click(e) {
      this.curIndex = e;
      console.log(e);
      // 想要动画捏
      d3.select('#maingroup').remove();
      this.generateVis();
    },
    change() {
      d3.select('#maingroup').remove();
      this.generateVis();
    },
    startselection()
    {
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
      let g = d3.select("#maingroup");
      console.log("selection start");
      const brush = d3.brush()
                      .on("start brush end",brushed);
      let that = this;
      function brushed({selection})
      {
        if(selection)
        {
          const [[x0,y0],[x1,y1]]=selection;
          that.selection = g.selectAll('circle')
                  .filter(d => x0 <= xScale(d.value) && xScale(d.value) < x1 && y0 <= yScale(d.word)+0.5*yScale.bandwidth() && yScale(d.word)+0.5*yScale.bandwidth() < y1)
                  .style("fill","gray")
                  .data();
        }
        else
        {
          g.selectAll('circle')
            .style("fill","black");
        }
      }
      g.call(brush);
    },
    endselection()
    {
      console.log("selection end"); 
      d3.select('#maingroup').on(".brush",null);
    }
  },
  mounted() {
    d3.csv("/李白.csv",function(data){
      console.log(data);
      this.databank[0] = data;
      this.generateVis();
    });
    //this.generateVis();
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
