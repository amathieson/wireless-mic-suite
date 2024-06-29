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
    }
  },
  methods: {
    fetch_scan: function (i) {
      fetch("https://localhost:7221/rfScan/235877949" + (i === 0 ? "?minFreq=578000000&maxFreq=638000000&stepSize=25000" : "")).then(
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
  <button @click="fetch_scan(0)">TEST Scan Frequencies</button>
  <hr/>
  <div class="fleet"></div>
</div>
</template>

<style scoped>
canvas {
  width: 100vw;
  max-height: 20em;
}
</style>