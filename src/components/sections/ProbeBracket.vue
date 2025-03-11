<script>
import { vue3dLoader } from "vue-3d-loader";

export default {
  components: {
    vue3dLoader
  }
};
</script>

<template>
  <div>
    <el-divider />

    <el-row justify="center">
      <h1 class="section-title">探头支架模型</h1>
    </el-row>
    <br>
    <el-row justify="space-evenly">
      <el-col :xs="24" :sm="6" :md="6" :lg="6" :xl="6">

        <vue3dLoader filePath="/freehand-3d-us-demo/public/model3d/bracket/clamper_right.stl"
          :cameraPosition="{ x: 80, y: 80, z: 80 }" :scale="{ x: 1, y: 1, z: 1 }" :position="{ x: 0, y: 0, z: 0 }"
          :rotation="{ x: 0, y: 0, z: 0 }" :enableDamping="true" :dampingFactor="0.05" :backgroundColor="0x000000">
        </vue3dLoader>
      </el-col>

      <el-col :xs="24" :sm="6" :md="6" :lg="6" :xl="6">
        <vue3dLoader filePath="/freehand-3d-us-demo/public/model3d/bracket/clamper_left.stl"
          :cameraPosition="{ x: 80, y: 80, z: 80 }" :scale="{ x: 1, y: 1, z: 1 }" :position="{ x: 0, y: 0, z: 0 }"
          :rotation="{ x: 0, y: 0, z: 0 }" :enableDamping="true" :dampingFactor="0.05" :backgroundColor="0x000000">
        </vue3dLoader>
      </el-col>

      <el-col :xs="24" :sm="6" :md="6" :lg="6" :xl="6">
        <vue3dLoader filePath="/freehand-3d-us-demo/public/model3d/bracket/IMU_adapter_vetical v4.stl"
          :cameraPosition="{ x: 80, y: 50, z: -100 }" :scale="{ x: 1, y: 1, z: 1 }" :position="{ x: 0, y: 0, z: 0 }"
          :rotation="{ x: 0, y: 0, z: 0 }" :enableDamping="true" :dampingFactor="0.05" :backgroundColor="0x000000">
        </vue3dLoader>
      </el-col>

    </el-row>
  </div>
</template>

<style>
.viewer-canvas {
  width: 100% !important;
  height: auto !important;
  aspect-ratio: 1 / 1 !important;
}
</style>