<template>
  <el-card class="flex-sub chart-item-container" :body-style="{padding: 0}" shadow="never">
    <template #header>
      <div class="text-bold">设备与固定资产空间分布总览</div>
    </template>
    <div id="mapView" class="chart-item"></div>
  </el-card>
</template>
<script>

export default {
  name: 'MapView',
  mounted() {

  },
  methods: {
    init() {
    }
  }
}
</script>

<style lang="scss" scoped>
.chart-item-container {
  width: 100%;
  height: calc(215px * 2 + 10px);
  .chart-item {
    height: calc(180px * 2);
  }
}
</style>
