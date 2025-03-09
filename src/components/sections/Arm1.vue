<script>

import { use } from 'echarts/core';
import { CanvasRenderer } from 'echarts/renderers';
import { LineChart, PieChart } from 'echarts/charts';
import {
  TitleComponent,
  TooltipComponent,
  LegendComponent,
  GridComponent
} from 'echarts/components';
import VChart, { THEME_KEY } from 'vue-echarts';
import { ref, provide, onMounted } from 'vue';
import { vue3dLoader } from "vue-3d-loader";
import { Niivue } from "@niivue/niivue";

use([
  CanvasRenderer,
  LineChart,
  TitleComponent,
  TooltipComponent,
  LegendComponent,
  GridComponent
]);

function generateOption(titleText) {
  return {
    title: {
      text: titleText,
      left: 'center',
      top: '5%'
    },
    tooltip: {
      trigger: 'axis'
    },
    legend: {
      orient: 'horizontal',
      left: 'center',
      top: '80%',
      data: ['IMU-1', 'IMU-2', 'IMU-3', 'IMU-4']
    },
    grid: {
      left: '3%',
      right: '4%',
      bottom: '20%',
      top: '20%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      boundaryGap: false
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        name: 'IMU-1',
        type: 'line',
        data: []
      },
      {
        name: 'IMU-2',
        type: 'line',
        data: []
      },
      {
        name: 'IMU-3',
        type: 'line',
        data: []
      },
      {
        name: 'IMU-4',
        type: 'line',
        data: []
      }
    ]
  };
}

export default {
  components: {
    VChart,
    vue3dLoader
  },
  setup() {
    provide('THEME_KEY', 'light');

    const option_acc_x = ref(generateOption('IMU 加速度 X'));
    const option_acc_y = ref(generateOption('IMU 加速度 Y'));
    const option_acc_z = ref(generateOption('IMU 加速度 Z'));
    const option_ang_x = ref(generateOption('IMU 欧拉角 X'));
    const option_ang_y = ref(generateOption('IMU 欧拉角 Y'));
    const option_ang_z = ref(generateOption('IMU 欧拉角 Z'));

    onMounted(async () => {
      try {
        // imu
        const jsonResponse = await fetch('/freehand-3d-us-demo/public/result/arm_160/160.json');
        const data = await jsonResponse.json();

        const x = [];
        for (let i = 0; i < data.imu1.length; i++) {
          x.push(i);
        }

        option_acc_x.value.xAxis.data = x;
        option_acc_y.value.xAxis.data = x;
        option_acc_z.value.xAxis.data = x;
        option_ang_x.value.xAxis.data = x;
        option_ang_y.value.xAxis.data = x;
        option_ang_z.value.xAxis.data = x;

        for (const [i, k] of ['imu1', 'imu2', 'imu3', 'imu4'].entries()) {
          option_acc_x.value.series[i].data = data[k].map(item => item[0]);
          option_acc_y.value.series[i].data = data[k].map(item => item[1]);
          option_acc_z.value.series[i].data = data[k].map(item => item[2]);
          option_ang_x.value.series[i].data = data[k].map(item => item[3]);
          option_ang_y.value.series[i].data = data[k].map(item => item[4]);
          option_ang_z.value.series[i].data = data[k].map(item => item[5]);
        }

        const nv_gt = new Niivue()
        nv_gt.attachTo('gl-gt')
        nv_gt.loadVolumes([{ url: "/freehand-3d-us-demo/result/arm_160/160_real.nii.gz" }])

        const nv_pred = new Niivue()
        nv_pred.attachTo('gl-pred')
        nv_pred.loadVolumes([{ url: "/freehand-3d-us-demo/result/arm_160/160_fake.nii.gz" }])

      } catch (error) {
        console.error(error);
      }
    });

    return {
      option_acc_x,
      option_acc_y,
      option_acc_z,
      option_ang_x,
      option_ang_y,
      option_ang_z,
    };
  }
};

</script>

<template>
  <div>
    <el-divider />

    <el-row justify="center">
      <h1 class="section-title">示例1（手臂）</h1>
    </el-row>

    <br>

    <el-row justify="center" class="equal-height-row" gutter="20">

      <!-- chart -->
      <el-col :xs="8" :sm="8" :md="8" :lg="8" :xl="8">
        <div class="element-container">
          <v-chart class="chart" :option="option_acc_x" autoresize />
          <v-chart class="chart" :option="option_ang_x" autoresize />
        </div>
      </el-col>

      <el-col :xs="8" :sm="8" :md="8" :lg="8" :xl="8">
        <div class="element-container">
          <v-chart class="chart" :option="option_acc_y" autoresize />
          <v-chart class="chart" :option="option_ang_y" autoresize />
        </div>
      </el-col>

      <el-col :xs="8" :sm="8" :md="8" :lg="8" :xl="8">
        <div class="element-container">
          <v-chart class="chart" :option="option_acc_z" autoresize />
          <v-chart class="chart" :option="option_ang_z" autoresize />
        </div>
      </el-col>

    </el-row>

    <el-row justify="center" class="equal-height-row" gutter="20">

      <!-- video -->
      <el-col :xs="24" :sm="6" :md="6" :lg="6" :xl="6">
        <p class="caption">超声扫查序列</p>
        <div class="element-container">
          <el-container class="video-container">
            <video controls muted preload playsinline>
              <source src="/freehand-3d-us-demo/result/arm_160/160.mp4" type="video/mp4">
            </video>
          </el-container>
        </div>
      </el-col>

      <!-- ground truth -->
      <el-col :xs="24" :sm="9" :md="9" :lg="9" :xl="9">
        <p class="caption">超声扫查序列</p>
        <div class="element-container">
          <canvas id="gl-gt"></canvas>
        </div>
      </el-col>

      <!-- prediction -->
      <el-col :xs="24" :sm="9" :md="9" :lg="9" :xl="9">
        <p class="caption">超声扫查序列</p>
        <div class="element-container">
          <canvas id="gl-pred"></canvas>
        </div>
      </el-col>

    </el-row>

  </div>
</template>


<style scoped>
.chart {
  height: 250px;
  margin-top: 5px;
}

.video-container {
  margin: 0;
  width: 100%;
  height: 250px; 
  min-height: 300px; 
  overflow: hidden; 
}

iframe,
video {
  aspect-ratio: inherit;
  width: 100%;
  display: block;
  object-fit: fill;
}

.equal-height-row {
  display: flex;
  align-items: stretch;
}

.caption {
  margin-top: auto;
  text-align: center;
  font-size: 22px;
  color: black;
  font-weight: bold;
}

</style>