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

import vtkActor from '@kitware/vtk.js/Rendering/Core/Actor'
import vtkMapper from '@kitware/vtk.js/Rendering/Core/Mapper'

import vtkFullScreenRenderWindow from '@kitware/vtk.js/Rendering/Misc/FullScreenRenderWindow';
import vtkHttpDataSetReader from '@kitware/vtk.js/IO/Core/HttpDataSetReader';
import vtkXMLImageDataReader from '@kitware/vtk.js/IO/XML/XMLImageDataReader';
import vtkImageMarchingCubes from '@kitware/vtk.js/Filters/General/ImageMarchingCubes';

import '@kitware/vtk.js/favicon'
import '@kitware/vtk.js/Rendering/Profiles/Geometry'
import '@kitware/vtk.js/IO/Core/DataAccessHelper/HtmlDataAccessHelper'
import '@kitware/vtk.js/IO/Core/DataAccessHelper/HttpDataAccessHelper'
import '@kitware/vtk.js/IO/Core/DataAccessHelper/JSZipDataAccessHelper'

import '@kitware/vtk.js/Common/Core/StringArray'
import '@kitware/vtk.js/Common/DataModel/PolyData'

import '@kitware/vtk.js/Rendering/OpenGL/RenderWindow'
import '@kitware/vtk.js/Rendering/WebGPU/RenderWindow'

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


    const initRealVolume = () => {
      const container = document.getElementById('vtk-container-carotid-1-real');
      const fullScreenRenderWindow = vtkFullScreenRenderWindow.newInstance({
        container: container,
        background: [0, 0, 0],
      });
      const renderWindow = fullScreenRenderWindow.getRenderWindow();
      const renderer = fullScreenRenderWindow.getRenderer();

      // fullScreenRenderWindow.addController(controlPanel);

      const actor = vtkActor.newInstance();
      const mapper = vtkMapper.newInstance();
      const marchingCube = vtkImageMarchingCubes.newInstance({
        contourValue: 0.0,
        computeNormals: true,
        mergePoints: true,
      });

      actor.setMapper(mapper);
      mapper.setInputConnection(marchingCube.getOutputPort());

      const reader = vtkXMLImageDataReader.newInstance();
      marchingCube.setInputConnection(reader.getOutputPort());

      reader.setUrl(`/freehand-3d-us-demo/public/result/carotid_575/real_volume.vti`, { loadData: true }).then(() => {
          const data = reader.getOutputData();
          const dataRange = data.getPointData().getScalars().getRange();
          const firstIsoValue = (dataRange[0] + dataRange[1]) / 3;

          marchingCube.setContourValue(firstIsoValue);
          renderer.addActor(actor);
          renderer.getActiveCamera().set({ position: [-1, -1, 1], viewUp: [0, -1, 0] });
          renderer.resetCamera();
          renderWindow.render();
        });
    }

    const initFakeVolume = () => {
      const container = document.getElementById('vtk-container-carotid-1-fake');
      const fullScreenRenderWindow = vtkFullScreenRenderWindow.newInstance({
        container: container,
        background: [0, 0, 0],
      });
      const renderWindow = fullScreenRenderWindow.getRenderWindow();
      const renderer = fullScreenRenderWindow.getRenderer();

      // fullScreenRenderWindow.addController(controlPanel);

      const actor = vtkActor.newInstance();
      const mapper = vtkMapper.newInstance();
      const marchingCube = vtkImageMarchingCubes.newInstance({
        contourValue: 0.0,
        computeNormals: true,
        mergePoints: true,
      });

      actor.setMapper(mapper);
      mapper.setInputConnection(marchingCube.getOutputPort());

      const reader = vtkXMLImageDataReader.newInstance();
      marchingCube.setInputConnection(reader.getOutputPort());

      reader.setUrl(`/freehand-3d-us-demo/public/result/carotid_575/fake_volume.vti`, { loadData: true }).then(() => {
          const data = reader.getOutputData();
          const dataRange = data.getPointData().getScalars().getRange();
          const firstIsoValue = (dataRange[0] + dataRange[1]) / 3;

          marchingCube.setContourValue(firstIsoValue);
          renderer.addActor(actor);
          renderer.getActiveCamera().set({ position: [-1, -1, 1], viewUp: [0, -1, 0] });
          renderer.resetCamera();
          renderWindow.render();
        });
    }

    onMounted(async () => {
      try {
        // imu
        const jsonResponse = await fetch('/freehand-3d-us-demo/result/carotid_575/imu_data.json');
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

        initRealVolume();
        initFakeVolume();

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
      <h1 class="section-title">示例5（颈动脉）</h1>
    </el-row>

    <br>

    <el-row justify="center" class="equal-height-row" gutter="20">

      <!-- chart -->
      <el-col :xs="24" :sm="7" :md="7" :lg="7" :xl="7">
        <div class="element-container-1">
          <v-chart class="chart" :option="option_acc_x" autoresize />
          <v-chart class="chart" :option="option_ang_x" autoresize />
        </div>
      </el-col>

      <el-col :xs="24" :sm="7" :md="7" :lg="7" :xl="7">
        <div class="element-container-1">
          <v-chart class="chart" :option="option_acc_y" autoresize />
          <v-chart class="chart" :option="option_ang_y" autoresize />
        </div>
      </el-col>

      <el-col :xs="24" :sm="7" :md="7" :lg="7" :xl="7">
        <div class="element-container-1">
          <v-chart class="chart" :option="option_acc_z" autoresize />
          <v-chart class="chart" :option="option_ang_z" autoresize />
        </div>
      </el-col>

    </el-row>

    <el-row justify="center" class="equal-height-row" gutter="20">

      <!-- video -->
      <el-col :xs="24" :sm="7" :md="7" :lg="7" :xl="7">
        <p class="caption">超声扫查序列</p>
        <div class="element-container-2">
          <el-container class="video-container">
            <video controls muted preload autoplay loop playsinline>
              <source src="/result/carotid_575/video.webm" type="video/mp4">
            </video>
          </el-container>
        </div>
      </el-col>

      <!-- ground truth -->
      <el-col :xs="24" :sm="8" :md="8" :lg="8" :xl="8">
        <p class="caption">真实重建容积</p>
        <div class="element-container-2">
          <div id="vtk-container-carotid-1-real"></div>
        </div>
      </el-col>

      <!-- prediction -->
      <el-col :xs="24" :sm="8" :md="8" :lg="8" :xl="8">
        <p class="caption">预测重建容积</p>
        <div class="element-container-2">
          <div id="vtk-container-carotid-1-fake"></div>
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
  height: 260px;
  overflow: visible;
}

iframe,
video {
  aspect-ratio: 248 / 260;
  display: block;
  margin: 0 auto;
  object-fit: contain;
}

.equal-height-row {
  display: flex;
  align-items: stretch;
}

.caption {
  margin-top: 0px;
  margin-bottom: 5px;
  text-align: center;
  font-size: 22px;
  color: black;
  font-weight: bold;
}

#vtk-container-carotid-1-fake, 
#vtk-container-carotid-1-real {
  height: 260px;
}

.element-container {
  height: 260px;
}

</style>