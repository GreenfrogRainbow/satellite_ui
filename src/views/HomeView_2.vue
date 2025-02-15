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
import * as echarts from 'echarts';
import axios from 'axios'
import 'echarts-gl';

const flight_data = import('@/assets/flights.json')

onMounted(async () => {
  var ROOT_PATH = 'https://echarts.apache.org/examples';
  var chartDom = document.getElementById('mapContainer');
  var myChart = echarts.init(chartDom);
  var option;

  const re_data = ROOT_PATH + '/data-gl/asset/data/flights.json'
  await flight_data.then( data => {
    function getAirportCoord(idx) {
      return [data.airports[idx][3], data.airports[idx][4]];
    }
    var routes = data.routes.map(function (airline) {
      return [getAirportCoord(airline[1]), getAirportCoord(airline[2])];
    });
    myChart.setOption({
      backgroundColor: '#000',
      globe: {
        baseTexture: ROOT_PATH + '/data-gl/asset/world.topo.bathy.200401.jpg',
        heightTexture:
          ROOT_PATH + '/data-gl/asset/bathymetry_bw_composite_4k.jpg',
        shading: 'lambert',
        light: {
          ambient: {
            intensity: 0.4
          },
          main: {
            intensity: 0.4
          }
        },
        viewControl: {
          autoRotate: false
        }
      },
      series: {
        type: 'lines3D',
        coordinateSystem: 'globe',
        blendMode: 'lighter',
        lineStyle: {
          width: 1,
          color: 'rgb(50, 50, 150)',
          opacity: 0.1
        },
        data: routes
      }
    });

    option && myChart.setOption(option);
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
}

.top-bar {
  z-index: 10;
  width: 100%;
  height: 80px;
  background-color: #00000099;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
}

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
}

.home-buttons {
  height: 50%;
  width: auto;
  margin-left: 50px;
  font-size: 21px;
  color: white;
  background: linear-gradient(to right, #ffffff, #ffffff) no-repeat left bottom;
  background-size: 0 2px;
  transition: 0.5s all;
  cursor: pointer;
}

.home-buttons:hover {
  background-size: 100% 2px;
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
