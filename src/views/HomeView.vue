<template>
  <div class="main-box">
    <div class="top-bar">
      <div class="main-title">Starlink Source Map</div>
      <div class="right-buttons">
        <div class="home-buttons sign-up">SIGNUP</div>
        <div class="home-buttons log-in">LOGIN</div>
        <div class="home-buttons help">HELP</div>
      </div>
    </div>
    <div ref="mapContainer" id="mapContainer"></div>

  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'


// 地球半径（单位：公里）
const R_EARTH = 6371

// 工具函数：角度与弧度互转
const degToRad = deg => deg * Math.PI / 180
const radToDeg = rad => rad * 180 / Math.PI

/**
 * 计算卫星轨道的“过顶点”轨迹（ground track）。
 * 假设卫星为圆轨道，给定倾角、升交点赤经（RAAN）、初始近点角（anomaly）以及轨道高度（默认为 500 km）。
 * 返回值是 [lng, lat] 坐标数组，描述卫星在地球表面的投影轨迹。
 */
function computeOrbitPath(inclination, raan, anomaly, altitude = 500, numPoints = 3600) {
  const orbitRadius = R_EARTH + altitude
  const incRad = degToRad(inclination)
  const raanRad = degToRad(raan)
  const anomalyRad = degToRad(anomaly)
  let coordinates = []
  // 以 360° 均分计算轨道上各个点的位置
  for (let i = 0; i < numPoints; i++) {
    const theta = degToRad(i * (360 / numPoints)) + anomalyRad
    // 卫星在轨道平面内的初始坐标（此时轨道平面与赤道平面重合）
    const x_orb = orbitRadius * Math.cos(theta)
    const y_orb = orbitRadius * Math.sin(theta)
    const z_orb = 0
    // 旋转：将轨道平面绕 X 轴旋转以加入倾角
    const x1 = x_orb
    const y1 = y_orb * Math.cos(incRad)
    const z1 = y_orb * Math.sin(incRad)
    // 旋转：绕 Z 轴旋转以加入升交点赤经
    const x2 = x1 * Math.cos(raanRad) - y1 * Math.sin(raanRad)
    const y2 = x1 * Math.sin(raanRad) + y1 * Math.cos(raanRad)
    const z2 = z1
    // 计算该位置对应的地面投影：将卫星位置按比例投影到地球半径上
    const scale = R_EARTH / orbitRadius
    const x_ground = x2 * scale
    const y_ground = y2 * scale
    const z_ground = z2 * scale
    // 转换为经纬度：lat = arcsin(z/R), lon = atan2(y,x)
    const lat = radToDeg(Math.asin(z_ground / R_EARTH))
    const lon = radToDeg(Math.atan2(y_ground, x_ground))
    coordinates.push([lon, lat])
  }
  return coordinates
}

function computeOrbitPath3D(inclination, raan, anomaly, altitude = 500, numPoints = 3600) {
  const orbitRadius = R_EARTH + altitude;
  const incRad = degToRad(inclination);
  const raanRad = degToRad(raan);
  const anomalyRad = degToRad(anomaly);
  let coordinates = [];
  for (let i = 0; i < numPoints; i++) {
    const theta = degToRad(i * (360 / numPoints)) + anomalyRad;
    // 轨道平面中的初始位置
    const x_orb = orbitRadius * Math.cos(theta);
    const y_orb = orbitRadius * Math.sin(theta);
    const z_orb = 0;
    // 引入轨道倾角
    const x1 = x_orb;
    const y1 = y_orb * Math.cos(incRad);
    const z1 = y_orb * Math.sin(incRad);
    // 引入升交点赤经
    const x2 = x1 * Math.cos(raanRad) - y1 * Math.sin(raanRad);
    const y2 = x1 * Math.sin(raanRad) + y1 * Math.cos(raanRad);
    const z2 = z1;
    // 注意：此处不再缩放到地球半径，而是保持轨道半径（从而保留高度信息）
    // 根据 ECEF 坐标计算经纬度，注意这里用 orbitRadius 替代 R_EARTH
    const lat = radToDeg(Math.asin(z2 / orbitRadius));
    const lon = radToDeg(Math.atan2(y2, x2));
    // 可选：将高度作为第三个数值加入坐标（GeoJSON 标准支持 [lon, lat, altitude]）
    coordinates.push([lon, lat, altitude]);
  }
  return coordinates;
}


onMounted(() => {
  const map = new maplibregl.Map({
    container: 'mapContainer',
    // style: 'https://tiles.stadiamaps.com/styles/alidade_satellite.json',
    style: 'https://tiles.stadiamaps.com/styles/alidade_smooth_dark.json',
    center: [110, 40],
    zoom: 2,
  })

  map.addControl(new maplibregl.NavigationControl())

  map.on('load', () => {
    // 隐藏 symbol 图层（例如城市、地名等）
    const style = map.getStyle()
    if (style && style.layers) {
      style.layers.forEach(layer => {
        if (layer.type === 'symbol') {
          map.setLayoutProperty(layer.id, 'visibility', 'none')
        }
      })
    }
    // 开启 3D 地球模式
    map.setProjection({ type: 'globe' })

    // 定义多个卫星（可调整参数模拟不同轨道）
    const satellites = [
      {
        "id": "sat1",
        "inclination": 74,
        "raan": 156,
        "anomaly": 71
      },
      {
        "id": "sat2",
        "inclination": 61,
        "raan": 239,
        "anomaly": 70
      },
      {
        "id": "sat3",
        "inclination": 62,
        "raan": 269,
        "anomaly": 40
      },
      {
        "id": "sat4",
        "inclination": 36,
        "raan": 38,
        "anomaly": 141
      },
      {
        "id": "sat5",
        "inclination": 64,
        "raan": 354,
        "anomaly": 153
      },
      {
        "id": "sat6",
        "inclination": 66,
        "raan": 143,
        "anomaly": 70
      },
      {
        "id": "sat7",
        "inclination": 48,
        "raan": 335,
        "anomaly": 205
      },
      {
        "id": "sat8",
        "inclination": 30,
        "raan": 210,
        "anomaly": 76
      },
      {
        "id": "sat9",
        "inclination": 40,
        "raan": 324,
        "anomaly": 50
      },
      {
        "id": "sat10",
        "inclination": 71,
        "raan": 260,
        "anomaly": 3
      }
    ]

    // 计算 3D 轨迹（注意此处调用的是 computeOrbitPath3D）
    const orbitFeatures = satellites.map(sat => {
      const coords = computeOrbitPath3D(sat.inclination, sat.raan, sat.anomaly);
      return {
        type: "Feature",
        properties: { id: sat.id },
        geometry: {
          type: "LineString",
          coordinates: coords  // 此时每个坐标为 [lon, lat, altitude]
        }
      }
    });

    const orbitGeoJSON = {
      type: "FeatureCollection",
      features: orbitFeatures
    };

// 添加数据源及图层（但标准 line 图层不会显示高度效果）
    map.addSource('orbits3D', {
      type: 'geojson',
      data: orbitGeoJSON
    });

    map.addLayer({
      id: 'orbit3D-glow',
      type: 'line',
      source: 'orbits3D',
      layout: {},
      paint: {
        'line-color': '#00ffff',
        'line-width': 6,
        'line-blur': 5,
        'line-opacity': 0.8
      }
    });


    // 为每颗卫星创建 Marker，并保存运动相关的数据
    const satelliteMarkers = []
    satellites.forEach(sat => {
      const path = computeOrbitPath(sat.inclination, sat.raan, sat.anomaly)
      const el = document.createElement('img')
      el.src = './satellite2.png'  // 替换为实际的卫星图标路径
      el.style.width = '30px'
      el.style.height = '30px'
      el.style.transform = 'translate(-50%, -50%)'
      const marker = new maplibregl.Marker({ element: el })
        .setLngLat(path[0])
        .addTo(map)
      // 用浮点数记录位置进度，便于调整运动速度
      satelliteMarkers.push({ marker, path, index: 0 })
    })

    // 动画函数：更新所有卫星在各自轨道上的位置
    function animate() {
      satelliteMarkers.forEach(sat => {
        // 以更小的步长更新索引，降低旋转速度
        sat.index = (sat.index + 1) % sat.path.length
        const idx = Math.floor(sat.index)
        sat.marker.setLngLat(sat.path[idx])
      })
      requestAnimationFrame(animate)
    }
    animate()

    map.easeTo({
      center: [110, 40], // 跳转到该节点
      zoom: 3.5, // 放大地图以确保显示
      pitch: 70, // 调整俯仰角度
      bearing: 0, // 保持正北方向
      duration: 2500 // 动画持续时间
    })

  })

})
</script>

<style>
.main-box {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
  display: flex;
  flex-direction: column;
  .top-bar {
    z-index: 10;
    width: 100%;
    height: 80px;
    background-color: #00000099;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;

    .main-title {
      width: 400px;
      text-align: center;
      font-size: 30px;
      font-weight: 600;
      color: white;
      cursor: pointer;
    }
    .right-buttons {
      width: 600px;
      height: 100%;
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: center;

      .home-buttons {
        height: 50%;
        width: auto;
        margin-left: 50px;
        font-size: 21px;
        color: white;
        background: linear-gradient(to right, #ffffff,  #ffffff) no-repeat left bottom;
        background-size: 0 2px;
        transition: 0.5s all;
        cursor: pointer;
        &:hover {
          background-size: 100% 2px ;
        }
      }
    }
  }
}
#mapContainer {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
}
.maplibregl-control-container {
  display: none;
}
</style>
