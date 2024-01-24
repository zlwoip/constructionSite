<template>
  <el-card class="flex-sub chart-item-container" :body-style="{padding: 0}" shadow="never">
    <template #header>
      <div class="text-bold">网管设备辐射范围与空间分布总览</div>
    </template>
    <div class="chart-item">
      <div id="mapDom" class="map-container"></div>
      <div id="info" class="map-info"></div>
    </div>
  </el-card>
</template>
<script>
import * as THREE from 'three'
import * as maptalks from 'maptalks'
import { ThreeLayer, BaseObject } from 'maptalks.three'
import { E3Layer } from 'maptalks.e3'
import borderJsonData from '@/assets/data/hc_border.json'
import regionJsonData from '@/assets/data/hc_region.json'
import facula from '@/assets/data/facula.png'
import beam from '@/assets/data/beam.png'
class LightPillar extends BaseObject {
  constructor(coordinate, options, layer) {
    options = maptalks.Util.extend({}, {
      altitude: 0,
      width: 20,
      height: 200,
      chunk: 2,
      imgUrl: beam,
      color: 0x44ffaa,
      showParticles: true,
      particlesAmount: 10,
      particlesSize: 4,
      particlesSpeed: 0.05
    }, options, { layer, coordinate })
    super()
    this._initOptions(options)
    const { altitude, width, height, chunk, imgUrl, color, showParticles, particlesSize } = options

    const geometry = new THREE.PlaneGeometry(width, height)
    geometry.rotateX(Math.PI / 2)
    geometry.translate(0, 0, height / 2)
    const textureLoader = new THREE.TextureLoader()
    const material = new THREE.MeshBasicMaterial({
      map: textureLoader.load(imgUrl),
      color: color,
      transparent: true,
      side: THREE.DoubleSide,
      depthWrite: false
    })
    this._createGroup()
    const mesh = new THREE.Mesh(geometry, material)
    for (let i = 0; i < chunk; i++) {
      this.getObject3d().add(mesh.clone().rotateZ(Math.PI * i / chunk))
    }
    const z = layer.distanceToVector3(altitude, altitude).x
    const position = layer.coordinateToVector3(coordinate, z)
    this.getObject3d().position.copy(position)
    if (showParticles) {
      this.particlesCloud = this.createParticles(particlesSize, true, 0.8, true, true, 0xffffff)
      this.getObject3d().add(this.particlesCloud)
    }
  }
  createParticles(size, transparent, opacity, vertexColors, sizeAttenuation, color) {
    const geom = new THREE.BufferGeometry()
    const material = new THREE.PointsMaterial({
      size: size,
      transparent: transparent,
      opacity: opacity,
      vertexColors: vertexColors,
      sizeAttenuation: sizeAttenuation,
      color: color,
      map: new THREE.TextureLoader().load(facula),
      depthTest: false
    })
    let { width } = this.options
    const { height, particlesAmount, particlesSpeed } = this.options
    width *= 0.70710678118655
    const veticsFloat32Array = []
    const veticsColors = []
    this.particlesSpeedList = []
    for (let i = 0; i < particlesAmount; i++) {
      veticsFloat32Array.push(Math.random() * width - width / 2, Math.random() * width - width / 2, Math.random() * height)
      this.particlesSpeedList.push(particlesSpeed + Math.random() / 5)
      const randomColor = new THREE.Color('hsl(0,0%,' + Math.ceil(100 * (Math.random() * 0.5 + 0.5)) + '%)')
      veticsColors.push(randomColor.r, randomColor.g, randomColor.b)
    }
    geom.attributes.position = new THREE.Float32BufferAttribute(veticsFloat32Array, 3)
    geom.attributes.color = new THREE.Float32BufferAttribute(veticsColors, 3)
    const particlesCloud = new THREE.Points(geom, material)
    particlesCloud.verticesNeedUpdate = true
    return particlesCloud
  }
  _animation() {
    const height = this.options.height
    const positionArray = Array.from(this.particlesCloud.geometry.attributes.position.array)
    for (let i = 0; i < positionArray.length; i += 3) {
      positionArray[i + 2] += this.particlesSpeedList[i / 3]
      if (positionArray[i + 2] > height) {
        positionArray[i + 2] = 0
      }
    }
    this.particlesCloud.geometry.setAttribute('position', new THREE.Float32BufferAttribute(positionArray, 3))
  }
}
export default {
  name: 'MapView',
  data() {
    return {
      mapCenter: [122.08438, 37.43255],
      centerSite: [122.07887, 37.45754],
      altitude: 50,
      mapDom: null,
      currentFeature: null,
      globalBorder: null,
      regionPolygons: [],
      geoCoordMap: {},
      timeList: [],
      tableData: [],
      carouselIndex: 0,
      dataTimer: null
    }
  },
  mounted() {
    const now = new Date()
    const end = new Date(now.getFullYear(), now.getMonth(), now.getDate(), 23, 59, 59)
    const start = new Date(now.getFullYear(), now.getMonth(), now.getDate())
    start.setTime(start.getTime() - 3600 * 1000 * 24)
    this.timeList = [start, end]
    this.$nextTick(() => {
      this.initMapTalk()
      this.initBorderData()
      this.initRegionData()
      this.drawE3LayerDataToGlobal()
      setTimeout(() => {
        this.drawThreeLayerData()
      }, 1500)
      this.loadData()
    })
  },
  destroyed() {
    if (this.dataTimer) {
      clearInterval(this.dataTimer)
      this.dataTimer = null
    }
  },
  methods: {
    loadData() {
      this.tableList = []
      this.$post({
        url: this.$urlPath.showAlarm,
        data: {
          startDate: this.getDateTime(this.timeList[0]),
          endDate: this.getDateTime(this.timeList[1])
        }
      }).then((res) => {
        this.tableData = res.alarmInfoList || []
        const infoDom = document.getElementById('info')
        if (this.tableData.length) {
          this.setCarouselInfo()
          this.dataTimer = setInterval(() => {
            this.setCarouselInfo()
          }, 1000 * 5)
        } else {
          infoDom.innerHTML = `当前无报警设备，所有网管设备正常`
        }
      })
    },
    initMapTalk() {
      this.mapDom = new maptalks.Map('mapDom', {
        center: this.mapCenter,
        zoom: 12.6,
        spatialReference: {
          projection: 'EPSG:3857'
        },
        pitch: 41.6,
        dragRotate: false,
        attribution: false,
        baseLayer: new maptalks.VectorLayer('baseVector'),
        layers: [
          new maptalks.TileLayer('baseTitle', {
            // urlTemplate: 'http://wprd0{s}.is.autonavi.com/appmaptile?lang=zh_cn&size=1&style=7&z={z}&x={x}&y={y}&scl=2&style=6',
            urlTemplate: 'http://webst{s}.is.autonavi.com/appmaptile?style=6&x={x}&y={y}&z={z}',
            subdomains: ['01', '02', '03', '04'],
            cssFilter: 'sepia(75%) invert(90%)',
            visible: false,
            forceRenderOnMoving: true,
            forceRenderOnZooming: true,
            forceRenderOnRotating: true
          }),
          new maptalks.VectorLayer('regionBorder', {
            forceRenderOnMoving: true,
            forceRenderOnZooming: true,
            forceRenderOnRotating: true,
            enableAltitude: true,
            zIndex: -99,
            drawAltitude: {
              polygonFill: 'rgba(30,144,255,1)',
              polygonOpacity: 0.35,
              lineWidth: 0
            }
          }),
          new maptalks.VectorLayer('regionPolygon', {
            enableAltitude: true,
            forceRenderOnMoving: true,
            forceRenderOnZooming: true,
            forceRenderOnRotating: true
          })
        ]
      })
      this.mapDom.on('click', param => {
        const infoDom = document.getElementById('info')
        infoDom.innerHTML = `坐标：${param.coordinate.toFixed(5).toArray().join(',')}，缩放：${this.mapDom.getZoom().toFixed(1)}，中心点：${[this.mapDom.getCenter().x.toFixed(5), this.mapDom.getCenter().y.toFixed(5)].join()}，倾斜：${this.mapDom.getPitch().toFixed(1)}`
      })

      const threeLayer = new ThreeLayer('three', {
        forceRenderOnMoving: true,
        forceRenderOnRotating: true,
        animation: true
      })
      threeLayer.prepareToDraw = function (gl, scene, camera) {
        const light = new THREE.DirectionalLight(0xffffff)
        light.position.set(0, -10, 10).normalize()
        scene.add(light)
        animation()
      }
      threeLayer.addTo(this.mapDom)
      function animation() {
        if (threeLayer._needsUpdate) {
          threeLayer.renderScene()
        }
        requestAnimationFrame(animation)
      }
    },
    initBorderData() {
      const borderFeatures = borderJsonData.features[0]
      const feature = new maptalks.MultiPolygon(borderFeatures.geometry.coordinates, {
        properties: borderFeatures.properties
      })
      this.globalBorder = feature
      let mask = this.mapDom.getLayer('baseTitle').getMask()
      if (!mask) {
        mask = new maptalks.MultiPolygon(feature.getCoordinates())
        // this.mapDom.getLayer('baseTitle').setMask(mask)
        this.mapDom.getLayer('baseTitle').setMask(mask).show()
      } else {
        mask.setCoordinates(feature.getCoordinates())
      }
      this.getBorderLinesFeature(
        feature.getCoordinates().map(d => {
          return d[0]
        }),
        feature.properties
      ).addTo(this.mapDom.getLayer('regionBorder'))
      mask.copy().setSymbol({
        shadowBlur: 35,
        lineColor: '#000',
        lineWidth: 10,
        lineJoin: 'round',
        polygonFillOpacity: 0
      }).addTo(this.mapDom.getLayer('baseVector'))
    },
    getBorderLinesFeature(coordinates, properties) {
      return new maptalks.MultiLineString(coordinates, {
        symbol: {
          lineColor: 'rgba(30,144,255,1)',
          lineWidth: 1,
          textPlacement: 'vertex'
        },
        properties: {
          altitude: -this.altitude * 25,
          id: properties.id,
          properties: properties
        }
      })
    },
    initRegionPolygons(idx, coordinates, properties) {
      const polygon = new maptalks.MultiPolygon(coordinates, {
        symbol: {
          lineWidth: 1,
          lineColor: 'rgba(70,133,180,0.6)',
          polygonFill: 'rgba(135,206,250,0.1)',
          polygonOpacity: 0.5
        },
        properties: {
          altitude: 10,
          id: properties.adcode,
          properties: properties
        }
      }).on('dblclick', e => {
        console.log(e.target)
        return false
      })
      this.regionPolygons.push(polygon)
    },
    initRegionData() {
      regionJsonData.features.forEach((g, i) => {
        const properties = g.properties
        const coordinates = g.geometry.coordinates
        this.initRegionPolygons(i, coordinates, properties)
        this.geoCoordMap[properties.name] = properties.centroid
      })
      this.mapDom.getLayer('regionPolygon').addGeometry(this.regionPolygons)
    },
    drawE3LayerDataToGlobal() {
      const provinceData = []
      const dataArr = []
      for (const key in this.geoCoordMap) {
        const geoCoord = this.geoCoordMap[key]
        provinceData.push({
          name: key,
          value: geoCoord.concat(10)
        })
        dataArr.push({
          coords: [geoCoord, this.centerSite]
        })
      }
      const option = {
        tooltip: {
          trigger: 'item',
          formatter(params) {
            if (params.data) {
              return `<div style="color:#909399;font-size:12px"><span style="color:#e54d42"> ${params.name} </span> 中心机房</div>`
            }
            return ''
          },
          borderColor: '#dc143c'
        },
        series: [
          {
            name: 'porCenter',
            type: 'effectScatter',
            coordinateSystem: 'geo',
            data: [{
              name: '市公司机房',
              value: this.centerSite.concat(46)
            }],
            symbolSize: 15,
            rippleEffect: {
              scale: 3,
              brushType: 'stroke'
            },
            label: {
              formatter: '{b}',
              distance: 12,
              fontSize: 14,
              position: 'right',
              show: true
            },
            itemStyle: {
              color: '#dc143c'
            },
            tooltip: {
              formatter(params) {
                if (params.data) {
                  return `<div style="color:#909399;font-size:12px">中心机房网管在线 <span style="color:#e54d42"> ${params.value[2]} </span></div>`
                }
                return ''
              },
              borderColor: '#dc143c'
            },
            zlevel: 3
          },
          {
            name: 'porSite',
            type: 'effectScatter',
            coordinateSystem: 'geo',
            data: provinceData,
            symbolSize: 5,
            label: {
              formatter: '{b}',
              position: 'right',
              show: true
            },
            itemStyle: {
              color: 'rgba(100,149,237,0.7)'
            },
            rippleEffect: {
              scale: 6,
              number: 2,
              period: 1.5,
              color: 'rgba(25,25,112,0.9)',
              brushType: 'fill'
            },
            zlevel: 2
          },
          {
            name: 'porLine',
            type: 'lines',
            effect: {
              show: true,
              constantSpeed: 50,
              trailLength: 0.2,
              color: 'rgba(255,255,255,0.3)',
              symbolSize: 4
            },
            lineStyle: {
              normal: {
                color: '#e54d42',
                width: 1,
                opacity: 0.03,
                curveness: 0.4
              }
            },
            tooltip: {
              show: false
            },
            data: dataArr,
            zlevel: 1
          }
        ]
      }
      const e3Layer = new E3Layer('e3', option).addTo(this.mapDom)
      e3Layer.getEChartsInstance().on('mouseover', obj => {
        if (obj.seriesType === 'effectScatter') {
          if (!this.currentFeature) {
            this.currentFeature = this.findFeatureByName(obj.name)
            this.currentFeature.updateSymbol({
              polygonFill: 'rgba(135,206,250,0.9)'
            })
          } else if (this.currentFeature.properties.properties.name !== obj.name) {
            this.currentFeature.updateSymbol({
              polygonFill: 'rgba(135,206,250,0.1)'
            })
            this.currentFeature = this.findFeatureByName(obj.name)
            this.currentFeature.updateSymbol({
              polygonFill: 'rgba(135,206,250,0.9)'
            })
          }
        }
      }).on('mouseout', obj => {
        if (obj.seriesType === 'effectScatter' && this.currentFeature) {
          this.currentFeature.updateSymbol({
            polygonFill: 'rgba(135,206,250,0.1)'
          })
          this.currentFeature = null
        }
      })
    },
    drawThreeLayerData() {
      const threeLayer = this.mapDom.getLayer('three')
      for (const key in this.geoCoordMap) {
        const Coordinate = this.geoCoordMap[key]
        threeLayer.addMesh(new LightPillar(new maptalks.Coordinate(Coordinate), {
          minZoom: 6,
          maxZoom: 14,
          width: 30,
          height: Math.ceil(120 * (Math.random() * 0.9 + 0.15)),
          color: '#6495ED'
        }, threeLayer))
      }
    },
    findFeatureByName(name) {
      for (let i = 0; i < this.regionPolygons.length; i++) {
        const item = this.regionPolygons[i]
        if (item.properties.properties.name === name) {
          return item
        }
      }
    },
    getDateTime(now) {
      if (!now) {
        now = new Date()
      }
      return `${now.getFullYear()}-${(now.getMonth() + 1).toString().padStart(2, '0')}-${now.getDate().toString().padStart(2, '0')} ${now.getHours().toString().padStart(2, '0')}:${now.getMinutes().toString().padStart(2, '0')}:${now.getSeconds().toString().padStart(2, '0')}`
    },
    setCarouselInfo() {
      const infoObj = this.tableData[this.carouselIndex % this.tableData.length]
      this.carouselIndex++
      const infoDom = document.getElementById('info')
      infoDom.innerHTML = `${infoObj.datetime} <span style="color:#d3d3d3"> 设备IP: </span> ${infoObj.machine} <span style="color:#d3d3d3"> 发生 </span> ${infoObj.description}`
    }
  }
}
</script>

<style lang="scss" scoped>
.chart-item-container {
  width: 100%;
  height: calc(215px * 3 + 15px);
  .chart-item {
    height: calc(205px * 3 + 8px);
    position: relative;
  }
}

.map-container {
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6);
}
.map-info {
  position: absolute;
  background-color: rgba(13, 13, 13, 0.5);
  padding: 0 10px;
  left: 0;
  top: 0;
  width: 100%;
  height: 13px;
  overflow: hidden;
  text-align: center;
  font: 13px bold sans-serif;
  line-height: 13px;
  color: #fff;
}
</style>
