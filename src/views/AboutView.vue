<template>
  <div class="main-box">
    <div class="top-bar">
      <div class="main-title">Flow Detection System</div>
      <div class="right-buttons">
        <div class="home-buttons sign-up">SIGNUP</div>
        <div class="home-buttons log-in">LOGIN</div>
        <div class="home-buttons help">HELP</div>
      </div>
    </div>
    <div ref="mapContainer" id="mapContainer"></div>
    <div class="text-box">
      <div class="top-contents">
        <div class="contents" style=" color: white;  font-size: 66px;">Starlink</div>
        <div class="contents"  style=" color: #EEA84E;font-size: 40px;">HIGH-SPEED INTERNET AROUND THE WORLD</div>
        <div class="contents" style=" color: #EEA84E;font-size: 40px;"></div>
      </div>
      <div class="go-in-button">
        <div class="content">Check more</div>
      </div>
    </div>
    <div class="mask-div"></div>

  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import {MapboxOverlay as DeckOverlay} from '@deck.gl/mapbox';
import {GeoJsonLayer, ArcLayer} from '@deck.gl/layers';

const satellite_data = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {
        "scalerank": 1,
        "type": "small",
        "name": "Sahnewal",
        "abbrev": "LUH",
        "location": "terminal",
        "gps_code": "VILD",
        "iata_code": "LUH",
        "wikipedia": "http://en.wikipedia.org/wiki/Sahnewal_Airport",
        "natlscale": 8,
        "featureclass": "Airport"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [20, -9.434460607584015],
        "decoordinates": [-160, 9.434460607584015]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "scalerank": 1,
        "type": "mid",
        "name": "Solapur",
        "abbrev": "SSE",
        "location": "terminal",
        "gps_code": "VASL",
        "iata_code": "SSE",
        "wikipedia": "http://en.wikipedia.org/wiki/Solapur_Airport",
        "natlscale": 8,
        "featureclass": "Airport"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [20, -9.434460607584015],
        "decoordinates": [-160, 9.434460607584015]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "scalerank": 1,
        "type": "mid",
        "name": "Birsa Munda",
        "abbrev": "IXR",
        "location": "terminal",
        "gps_code": "VERC",
        "iata_code": "IXR",
        "wikipedia": "http://en.wikipedia.org/wiki/Birsa_Munda_Airport",
        "natlscale": 8,
        "featureclass": "Airport"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [20, -39.434460607584015],
        "decoordinates": [-160, 39.434460607584015]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "scalerank": 1,
        "type": "mid",
        "name": "Ahwaz",
        "abbrev": "AWZ",
        "location": "terminal",
        "gps_code": "OIAW",
        "iata_code": "AWZ",
        "wikipedia": "http://en.wikipedia.org/wiki/Ahwaz_Airport",
        "natlscale": 8,
        "featureclass": "Airport"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [20, -49.434460607584015],
        "decoordinates": [-160, 49.434460607584015]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "scalerank": 1,
        "type": "mid and military",
        "name": "Gwalior",
        "abbrev": "GWL",
        "location": "terminal",
        "gps_code": "VIGR",
        "iata_code": "GWL",
        "wikipedia": "http://en.wikipedia.org/wiki/Gwalior_Airport",
        "natlscale": 8,
        "featureclass": "Airport"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [20, -69.434460607584015],
        "decoordinates": [-160, 69.434460607584015]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "scalerank": 1,
        "type": "mid",
        "name": "Hodeidah Int'l",
        "abbrev": "HOD",
        "location": "terminal",
        "gps_code": "OYHD",
        "iata_code": "HOD",
        "wikipedia": "http://en.wikipedia.org/wiki/Hodeida_International_Airport",
        "natlscale": 8,
        "featureclass": "Airport"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [20, -19.434460607584015],
        "decoordinates": [-160, 19.434460607584015]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "scalerank": 1,
        "type": "mid",
        "name": "Devi Ahilyabai Holkar Int'l",
        "abbrev": "IDR",
        "location": "terminal",
        "gps_code": "VAID",
        "iata_code": "IDR",
        "wikipedia": "http://en.wikipedia.org/wiki/Devi_Ahilyabai_Holkar_International_Airport",
        "natlscale": 8,
        "featureclass": "Airport"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [20, -22.434460607584015],
        "decoordinates": [-160, 22.434460607584015]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "scalerank": 1,
        "type": "mid",
        "name": "Gandhinagar",
        "abbrev": "ISK",
        "location": "ramp",
        "gps_code": "VANR",
        "iata_code": "ISK",
        "wikipedia": "http://en.wikipedia.org/wiki/Gandhinagar_Airport",
        "natlscale": 8,
        "featureclass": "Airport"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [20, 55.434460607584015],
        "decoordinates": [-160, -55.434460607584015]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "scalerank": 1,
        "type": "major and military",
        "name": "Chandigarh Int'l",
        "abbrev": "IXC",
        "location": "terminal",
        "gps_code": "VICG",
        "iata_code": "IXC",
        "wikipedia": "http://en.wikipedia.org/wiki/Chandigarh_Airport",
        "natlscale": 8,
        "featureclass": "Airport"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [20, 42.12121260129845],
        "decoordinates": [-160, -42.12121260129845]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "scalerank": 1,
        "type": "mid",
        "name": "Aurangabad",
        "abbrev": "IXU",
        "location": "terminal",
        "gps_code": "VAAU",
        "iata_code": "IXU",
        "wikipedia": "http://en.wikipedia.org/wiki/Aurangabad_Airport",
        "natlscale": 8,
        "featureclass": "Airport"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [20, -15.62245879377646],
        "decoordinates": [-160, 15.62245879377646]
      }
    },
  ]
}


onMounted(() => {
  const map = new maplibregl.Map({
    container: 'mapContainer',
    // style: 'https://tiles.stadiamaps.com/styles/alidade_satellite.json',
    style: 'https://tiles.stadiamaps.com/styles/alidade_smooth_dark.json',
    center: [-72, 0],
    zoom: 2.2,
  })

  const deckOverlay = new DeckOverlay({
    interleaved: true,
    layers: [
      new ArcLayer({
        id: 'arcs',
        data: satellite_data,
        dataTransform: d => d.features.filter(f => f.properties.scalerank < 4),
        // Styles
        getSourcePosition: f => f.geometry.decoordinates, // London
        getTargetPosition: f => f.geometry.coordinates,
        getSourceColor: [0, 128, 200],
        getTargetColor: [200, 0, 80],
        getWidth: 1,
        maxHeight: 800,
      })
    ]
  });

  map.addControl(deckOverlay);
  map.addControl(new maplibregl.NavigationControl());

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
  overflow: hidden;
  .top-bar {
    z-index: 12;
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
  .text-box {
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
    height: 100%;
    z-index: 11;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-evenly;
    .top-contents {
      width: 100%;
      height: 50%;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      .contents {
        transform: translateY(70px);
        font-weight: 700;
      }
    }
    .go-in-button {
      width: 100%;
      height: 50%;
      display: flex;
      flex-direction: column;
      align-items: center;
      .content {
        border-radius: 12px;
        padding:  5px 10px;
        transform: translateY(-100px);
        width: auto;
        font-size: 30px;
        color: white;
        background: linear-gradient(to right, #ffffff,  #ffffff) no-repeat left bottom;
        background-size: 0 2px;
        transition: 0.5s all;
        cursor: pointer;
        &:hover {
          background-color: #f2f2f288;
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
  transform: translateY(30%);
  transition: all 2s;
}
.mask-div {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
  z-index: 10;
}
.maplibregl-control-container {
  display: none;
}
</style>
