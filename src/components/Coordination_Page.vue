<script>
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend,
  LineController,
  BarController,
    Filler,
    ScatterController,
    LogarithmicScale
} from 'chart.js'
ChartJS.register(
    LinearScale,
    PointElement,
    LineElement,
    Title,
    Tooltip,
    Legend,
    LineController,
    BarController,
    ScatterController,
    LogarithmicScale,
    Filler
)

const lineMarkerText = {
  id: 'lineMarkerText',
  afterDatasetDraw: (chart, args, options) => {
    const {ctx, chartArea: {top, bottom, height}, scales: {x}} = chart;
    const RAD = Math.PI / 180;
    chart.data.mics.forEach((element, index) => {
      ctx.beginPath();
      ctx.strokeStyle = 'red';
      ctx.lineWidth = x.getPixelForValue(element.x + 25_000) - x.getPixelForValue(element.x);
      ctx.moveTo(x.getPixelForValue(element.x), top);
      ctx.lineTo(x.getPixelForValue(element.x), bottom);
      ctx.stroke();

      ctx.font = 'bold 12px sans-serif';
      ctx.translate(x.getPixelForValue(element.x), height / 2 + top)
      ctx.rotate(270 * RAD);
      ctx.textAlign = 'center';
      ctx.fillStyle = 'white';
      ctx.fillText(element.name, 0,0);
      ctx.rotate(-270 * RAD);
      ctx.translate(-x.getPixelForValue(element.x), -(height / 2 + top));
    })
  }
}

export default {
  name: "Coordination_Page",
  mounted() {

  },
  data: ()=>{
    return {
      chart: null,
      catalogue: [
        {
          "name":"Shure UR1/UR2 J5E",
          "ranges": [
              [578_000_000,638_000_000]
          ],
          "bandwidth":25_000,
          "id":"SHURE_J5E"
        },
        {
          "name":"Sennheiser EW-DX Q1-9",
          "ranges": [
              [470_200_000,550_000_000]
          ],
          "bandwidth":200_000,
          "id":"SENNHEISER_EW_Q"
        },
        {
          "name":"Sennheiser EW100 G3 A",
          "ranges": [
              [516_000_000,558_000_000]
          ],
          "bandwidth":42_000_000,
          "id":"SENNHEISER_EW_G3_A"
        },
        {
          "name":"Sennheiser EW100 G3 B",
          "ranges": [
              [626_000_000,668_000_000]
          ],
          "bandwidth":42_000_000,
          "id":"SENNHEISER_EW_G3_B"
        },
        {
          "name":"Sennheiser EW100 G3 G",
          "ranges": [
              [566_000_000,608_000_000]
          ],
          "bandwidth":42_000_000,
          "id":"SENNHEISER_EW_G3_G"
        },
        {
          "name":"Sennheiser XSW A",
          "ranges": [
              [548_000_000,572_000_000]
          ],
          "bandwidth":24_000_000,
          "id":"SENNHEISER_XSW_A"
        },
        {
          "name":"Sennheiser XSW GB",
          "ranges": [
              [606_000_000,630_000_000]
          ],
          "bandwidth":24_000_000,
          "id":"SENNHEISER_XSW_GB"
        },
        {
          "name":"Sennheiser XSW B",
          "ranges": [
              [614_000_000,638_000_000]
          ],
          "bandwidth":24_000_000,
          "id":"SENNHEISER_XSW_B"
        },
        {
          "name":"Sennheiser XSW BC",
          "ranges": [
              [670_000_000,694_000_000]
          ],
          "bandwidth":24_000_000,
          "id":"SENNHEISER_XSW_BC"
        },
        {
          "name":"Sennheiser XSW C",
          "ranges": [
              [766_000_000,790_000_000]
          ],
          "bandwidth":24_000_000,
          "id":"SENNHEISER_XSW_C"
        },
        {
          "name":"Sennheiser XSW D",
          "ranges": [
              [794_000_000,806_000_000]
          ],
          "bandwidth":24_000_000,
          "id":"SENNHEISER_XSW_D"
        },
        {
          "name":"Sennheiser XSW E",
          "ranges": [
              [821_000_000,832_000_000],
              [863_000_000,865_000_000]
          ],
          "bandwidth":24_000_000,
          "id":"SENNHEISER_XSW_E"
        },
        {
          "name":"Sennheiser XSW K",
          "ranges": [
              [925_000_000,937_500_000],
          ],
          "bandwidth":24_000_000,
          "id":"SENNHEISER_XSW_K"
        }
      ],
    }
  },
  methods: {
    fetch_scan: function (i) {
      fetch("https://localhost:7221/rfScan/777995160" + (i === 0 ? "?minFreq=578000000&maxFreq=638000000&stepSize=25000" : "")).then(
          (response) => {response.json().then((data) => {
            if (data.samples.length < 1 && i < 100)
              setTimeout(()=>{this.fetch_scan(i+1)}, 1000);
            else {
              if (this.$data.chart !== null) {
                this.$data.chart.destroy();
              }
                const formatter = new Intl.NumberFormat('en-GB', { minimumFractionDigits: 2 });
                this.$data.chart = new ChartJS(this.$refs.spectrum, {
                  data: {
                    datasets: [
                      {
                        type: 'line',
                        label: 'Frequency Scan',
                        data: data.samples.map((a) => {
                          return {x: a.frequency, y: Math.pow(10, (a.strength / 10))}
                        }),
                        fill: {
                          target:{value:Math.pow(10, (-110 / 10))},
                          above: "#E0A100"
                        },

                      }],
                    mics: [
                      {x:578500000, name:"01 John"}
                    ]
                  },
                  options: {
                    scales: {
                      y: {
                        type: 'logarithmic',
                        position: 'left',
                        max: Math.pow(10, (-30 / 10)),
                        min: Math.pow(10, (-120 / 10)),
                        ticks: {
                          color: '#CDD1DE',
                          callback: function (value, index, values) {
                            // Convert the logarithmic value back to dBm for display
                            return Math.round(10 * Math.log10(value)) + ' dBm';
                          },
                        },
                        grid: {
                          color: '#CDD1DE'
                        }
                      },
                      x: {
                        type: 'linear',
                        position: 'bottom',
                        ticks: {
                          color: '#CDD1DE',
                          callback: function(value, index, values) {
                            // Convert the logarithmic value back to dBm for display
                            return formatter.format(value/1_000_000) + ' MHz';
                          },
                        },
                        grid: {
                          color: '#CDD1DE'
                        }
                      }
                    },
                    backgroundColor: '#FFBA0A',
                    borderColor: '#FFBA0A',
                    showLine: true,
                    pointStyle: false,
                    plugins: {
                      legend: {
                        labels: {
                          color: '#CDD1DE'
                        }
                      }
                    }
                  },
                  plugins: [lineMarkerText]
                })
              }
          })}
      )
    }
  }
}
</script>

<template>
<div class="page-container">
  <canvas ref="spectrum"></canvas>
  <button @click="fetch_scan(0)"><span class="material-symbols-outlined">bug_report</span>TEST Scan Frequencies</button>
  <button @click=""><span class="material-symbols-outlined">radar</span> Scan Frequencies</button>
  <button @click=""><span class="material-symbols-outlined">pin</span> Calculate Frequencies</button>
  <button @click=""><span class="material-symbols-outlined">deployed_code_update</span> Deploy Changes</button>
  <div class="lower-zone">
    <div class="fleet">
      <h2><span class="material-symbols-outlined">dns</span> Wireless Fleet</h2>
      <ul>
        <li v-for="i in 20">
          <a href="#"><span class="material-symbols-outlined">lock</span></a>
          <input type="text" value="01 John" placeholder="Transmitter Name">
          <input type="text" value="578.50" placeholder="000.00 MHz">
        </li>
      </ul>
    </div>
    <div class="network">
      <h2><span class="material-symbols-outlined">lan</span>Network Devices</h2>
      <ul>
        <li v-for="i in 10" class="inventory-item">
          <a href="#"><span class="material-symbols-outlined">add</span></a>
          01 John <code>578.50 MHz</code>
        </li>
      </ul>
    </div>
    <div class="catalogue">
      <h2><span class="material-symbols-outlined">book_5</span>Device Catalogue</h2>
      <ul>
        <li class="inventory-item">
          <a href="#"><span class="material-symbols-outlined">add</span></a>
          Custom Device
        </li>
        <li v-for="i in catalogue" class="inventory-item">
          <a href="#"><span class="material-symbols-outlined">add</span></a>
          {{i.name}}
        </li>
      </ul>
    </div>
  </div>
</div>
</template>

<style scoped>
canvas {
  width: 100%;
  max-height: 20em;
}
.lower-zone {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr;
  grid-template-areas: "a b" "a c";
  max-height: calc(100vh - var(--bar-height) - 27em);
  text-align: left;
  gap: 0.5em ;
  margin-top: 1em;
  &>* {
    border: var(--text-200) 2px solid;
    border-radius: 0.8em;
    padding: 1em;
    overflow: hidden;
    &>ul {
      overflow-y: auto;
    }
    &>h2 {
      text-align: left;
      font-size: medium;
      display: inline-flex;
      margin: 0;
      gap: 0.5em;
      &>span {
        color: var(--primary-500);
      }
    }
  }
}
.fleet {
  grid-area: a;
}
.network {
  grid-area: b;
  overflow: hidden;
}
.catalogue {
  grid-area: c;
  overflow: hidden;
}
ul {
  margin: 0;
  height: calc(100% - 1.5em);
  overflow-y: auto;
  overflow-x: hidden;
}
.inventory-item {
  list-style: none;
  display: flex;
  align-items: baseline;
  gap: 0.5em;
  margin: 0.25em 0;
  cursor: pointer;
  border-bottom: var(--text-200) 1px solid;
  &>a {
    align-self: center;
  }
  & > a > .material-symbols-outlined {
    color: var(--text-200);
  }
  transition: background-color 250ms ease, transform 250ms ease;
}
.inventory-item:last-of-type {
  border-bottom: none;
}
.inventory-item:hover {
  background-color: var(--dark-200);
}
.inventory-item:active {
  transform: scale(0.99);
}
.fleet > ul {
  list-style: none;
  &>li {
    display: flex;
    gap: 0.5em;
    align-items: baseline;
    &>a {
      align-self: center;
    }
  }
}
</style>