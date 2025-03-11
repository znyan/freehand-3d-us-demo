<script setup>
import introduction from '../mds/introduction.mdx';
</script>

<template>
  <div>
    <el-divider />

    <el-row justify="center">
      <el-col :xs="24" :sm="20" :md="16" :lg="12" :xl="12">
        <div class="mdx-content">
          <introduction />
        </div>
      </el-col>
    </el-row>

    <el-row justify="center">
      <el-col :xs="24" :sm="20" :md="20" :lg="20" :xl="20">
        <el-image src="/freehand-3d-us-demo/public/introduction.png" fit="cover" class="mt-4" style="width: 100%;" />
      </el-col>
    </el-row>

  </div>
</template>

<style scoped>
.mdx-content :deep(p) {
  font-size: 20px;
  line-height: 1.6;
}
</style>