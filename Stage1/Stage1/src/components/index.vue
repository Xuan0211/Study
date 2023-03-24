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
              <el-dropdown-item>杜甫</el-dropdown-item>
              <el-dropdown-item>白居易</el-dropdown-item>
              <el-dropdown-item>李白</el-dropdown-item>
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

export default {
  name: 'index',
  data() {
    return {
      var1: '',
      var2: ''
    };
  },
  methods: {
    draw() {
      let margin = 30; // 上下左右边距

      let svg = d3.select('svg');
      let width = svg.attr('width');
      let height = svg.attr('height');

      // 创建矩形分组
      let g = svg.append('g')
        .attr('transform', 'translate(' + margin + ',' + margin + ')'); // 图表距离视口的左、上距离

      // 定义 X 轴比例尺
      let scaleX = d3.scaleBand()
        .domain(d3.range(this.data.length))
        .rangeRound([0, width - margin * 2]);

      // 定义 y 轴比例尺
      let scaleY = d3.scaleLinear()
        .domain([0, d3.max(this.data)])
        .range([height - margin * 2, 0]);
      // 上边距30；注意：range 后面跟的参数0，放在第二位 因为 y轴正方向向下

      // 绘制 x y 轴
      let axisX = d3.axisBottom(scaleX);
      let axisY = d3.axisLeft(scaleY);
      g.append('g').attr('transform', 'translate(0, ' + (height - margin * 2) + ')').call(axisX)
      g.append('g').attr('transform', 'translate(0,0)').call(axisY);

      // 创建矩形分组
      let gs = g.selectAll('rect')
        .data(this.data)
        .enter()
        .append('g');

      // 绘制矩形 + 过渡效果
      let rectP = 40; // 柱状图间距
      gs.append('rect')
        .attr('x', function (d, i) {
          return scaleX(i) + rectP / 2;
        })
        .attr('y', function (d, i) {
          var min = scaleY.domain()[0]; // [0, 73]
          return scaleY(min);
          // scaleY(0) y轴比例尺映射出来的是最大值；这个效果等同于 return height - 2*margin 的效果
        })
        .attr('width', function (d, i) {
          return scaleX.step() - rectP;
        })
        .attr('height', function (d, i) {
          return 0; // 动画初始状态为0
        })
        .attr('fill', 'pink')
        .transition()
        .duration(1500)
        .delay(function (d, i) {
          return i * 200 // 每个柱子逐渐开始的效果
        })
        .attr('y', function (d, i) {
          return scaleY(d)
        })
        .attr('height', function (d, i) {
          return height - margin * 2 - scaleY(d)
        })

      // 添加鼠标划入划出事件
      gs.on('mouseover', function () {
        d3.select(this.firstChild) // 这里的this是包含：rect text 的节点
          .transition()
          .duration(1000)
          .delay(200)
          .attr('fill', '#306ade');
      })

      gs.on('mouseout', function () {
        d3.select(this.firstChild)
          .transition()
          .duration(1000)
          .delay(200)
          .attr('fill', 'pink');
      })

      // 绘文字 + 过渡效果
      gs.append('text')
        .attr('x', function (d, i) {
          return scaleX(i) + rectP;
        })
        .attr('y', function (d, i) {
          return height - 2 * margin;
        })
        .attr('dx', function (d, i) {
          return -2;
        })
        .attr('dy', function (d, i) {
          return 20;
        })
        .text(function (d, i) {
          return d;
        })
        .attr('fill', 'green')
        .transition()
        .duration(1500)
        .delay(function (d, i) {
          return i * 200;
        })
        .attr('y', function (d, i) {
          return scaleY(d)
        })
    }
  },
  mounted() {
    this.draw();
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
