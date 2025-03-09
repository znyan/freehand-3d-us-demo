<script>

import { use } from 'echarts/core';
import { CanvasRenderer } from 'echarts/renderers';
import { PieChart } from 'echarts/charts';
import {
  TitleComponent,
  TooltipComponent,
  LegendComponent,
} from 'echarts/components';
import VChart, { THEME_KEY } from 'vue-echarts';
import { ref, provide } from 'vue';
import { vue3dLoader } from "vue-3d-loader";

use([
  CanvasRenderer,
  PieChart,
  TitleComponent,
  TooltipComponent,
  LegendComponent,
]);

export default {
  components: {
    VChart,
    vue3dLoader,
  },
  setup() {
    provide('THEME_KEY', 'light');
  },
  data() {
    return {
      option: {
        // 使用echarts设置属性和数据
        title: {
            text: 'Traffic Sources',
            left: 'center',
        },
        tooltip: {
            trigger: 'item',
            formatter: '{a} <br/>{b} : {c} ({d}%)',
        },
        legend: {
            orient: 'horizontal',
            left: 'right',
            top: 'bottom',
            data: ['Direct', 'Email', 'Ad Networks', 'Video Ads', 'Search Engines'],
        },
        series: [
          {
            name: 'Traffic Sources',
            type: 'pie',
            radius: '50%',
            center: ['50%', '50%'],
            data: [
              { value: 335, name: 'Direct' },
              { value: 310, name: 'Email' },
              { value: 234, name: 'Ad Networks' },
              { value: 135, name: 'Video Ads' },
              { value: 1548, name: 'Search Engines' },
            ],
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: 'rgba(0, 0, 0, 0.5)',
              },
            },
          },
        ],
      }
    }
  }
}

</script>

<template>
  <div>
    <el-divider />

    <el-row justify="center">
      <h1 class="section-title">示例2（颈动脉）</h1>
    </el-row>

    <br>

    <el-row justify="center" class="equal-height-row" gutter="20">
      <!-- video -->
      <el-col :xs="24" :sm="6" :md="6" :lg="6" :xl="6">
        <div class="element-container">
          <el-container class="video-container">
            <video controls muted preload playsinline>
              <source src="/video/video.mp4" type="video/mp4">
            </video>
          </el-container>
          <p class="caption">超声扫查序列</p>
        </div>
      </el-col>

      <!-- chart -->
      <el-col :xs="24" :sm="6" :md="6" :lg="6" :xl="6">
        <div class="element-container">
          <v-chart class="chart" :option="option" autoresize />
          <p class="caption">IMU测量数据</p>
        </div>
      </el-col>

      <!-- ground truth -->
      <el-col :xs="24" :sm="6" :md="6" :lg="6" :xl="6">
        <div class="element-container">
          <div class="threed-container">
            <vue3dLoader
              filePath="./model3d/obj/male02.obj"
              mtlPath="./model3d/obj/male02.mtl"
              outputEncoding="sRGB"
              :cameraPosition="{ x: 0, y: 0, z: 300 }"
              :scale="{ x: 1, y: 1, z: 1 }"
              :position="{ x: 0, y: 0, z: 0 }"
              :rotation="{ x: 0, y: 0, z: 0 }"
              :enableDamping="true"
              :dampingFactor="0.05"
              :backgroundColor="'#f2f2f2'"
            />
          </div>
          <p class="caption">真实重建容积</p>
        </div>
      </el-col>

      <!-- prediction -->
      <el-col :xs="24" :sm="6" :md="6" :lg="6" :xl="6">
        <div class="element-container">
          <vue3dLoader
            filePath="./model3d/ply/Lucy100k.ply"
            :cameraPosition="{ x: 100, y: 200, z: 3000 }"
            :scale="{ x: 1, y: 1, z: 1 }"
            :position="{ x: 100, y: 100, z: 100 }"
            :rotation="{ x: 0, y: 3, z: 0 }"
            :enableDamping="true"
            :dampingFactor="0.05"
            :backgroundColor="0xeeeeee"
          />
          <p class="caption">预测重建容积</p>
        </div>
      </el-col>
    </el-row>
  </div>
</template>


<style scoped>

.video-container{
  margin: 20px 0px 0px 0px;
  display: flex;
  justify-content: center;
  align-items: center;
}

iframe, video {
  aspect-ratio: 16 / 9;
  width: 100%;
  display: center;
}

.equal-height-row {
  display: flex;
  align-items: stretch;
}

.element-container {
  display: flex;
  flex-direction: column;
  height: 100%;
}

.element-container .caption {
  margin-top: auto;
  text-align: center;
  font-size: 24px; 
}
</style>