<script>
import {
  BarController,
  Chart as ChartJS,
  Filler,
  Legend,
  LinearScale,
  LineController,
  LineElement,
  LogarithmicScale,
  PointElement,
  ScatterController,
  Title,
  Tooltip
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
      element.x = element.frequency;
      let trans_type = catalogue.find((el) => el.id === element.type);
      ctx.beginPath();
      ctx.strokeStyle = 'red';
      ctx.lineWidth = x.getPixelForValue(element.x + 250_000) - x.getPixelForValue(element.x);
      ctx.moveTo(x.getPixelForValue(element.x), top);
      ctx.lineTo(x.getPixelForValue(element.x), bottom);
      ctx.stroke();

      ctx.font = 'bold 12px sans-serif';
      ctx.translate(x.getPixelForValue(element.x), height / 2 + top)
      ctx.rotate(270 * RAD);
      ctx.textAlign = 'center';
      ctx.fillStyle = 'white';
      ctx.fillText(element.name, 0, 0);
      ctx.rotate(-270 * RAD);
      ctx.translate(-x.getPixelForValue(element.x), -(height / 2 + top));
    })
  }
}
const formatter = new Intl.NumberFormat('en-GB', {minimumFractionDigits: 2});
const catalogue = [
  {
    "name": "Shure UR1/UR2 J5E",
    "ranges": [
      [578_000_000, 638_000_000]
    ],
    "bandwidth": 25_000,
    "id": "SHURE_J5E"
  },
  {
    "name": "Sennheiser EW-DX Q1-9",
    "ranges": [
      [470_200_000, 550_000_000]
    ],
    "bandwidth": 200_000,
    "id": "SENNHEISER_EW_Q"
  },
  {
    "name": "Sennheiser EW100 G3 A",
    "ranges": [
      [516_000_000, 558_000_000]
    ],
    "bandwidth": 42_000_000,
    "id": "SENNHEISER_EW_G3_A"
  },
  {
    "name": "Sennheiser EW100 G3 B",
    "ranges": [
      [626_000_000, 668_000_000]
    ],
    "bandwidth": 42_000_000,
    "id": "SENNHEISER_EW_G3_B"
  },
  {
    "name": "Sennheiser EW100 G3 G",
    "ranges": [
      [566_000_000, 608_000_000]
    ],
    "bandwidth": 42_000_000,
    "id": "SENNHEISER_EW_G3_G"
  },
  {
    "name": "Sennheiser XSW A",
    "ranges": [
      [548_000_000, 572_000_000]
    ],
    "bandwidth": 24_000_000,
    "id": "SENNHEISER_XSW_A"
  },
  {
    "name": "Sennheiser XSW GB",
    "ranges": [
      [606_000_000, 630_000_000]
    ],
    "bandwidth": 24_000_000,
    "id": "SENNHEISER_XSW_GB"
  },
  {
    "name": "Sennheiser XSW B",
    "ranges": [
      [614_000_000, 638_000_000]
    ],
    "bandwidth": 24_000_000,
    "id": "SENNHEISER_XSW_B"
  },
  {
    "name": "Sennheiser XSW BC",
    "ranges": [
      [670_000_000, 694_000_000]
    ],
    "bandwidth": 24_000_000,
    "id": "SENNHEISER_XSW_BC"
  },
  {
    "name": "Sennheiser XSW C",
    "ranges": [
      [766_000_000, 790_000_000]
    ],
    "bandwidth": 24_000_000,
    "id": "SENNHEISER_XSW_C"
  },
  {
    "name": "Sennheiser XSW D",
    "ranges": [
      [794_000_000, 806_000_000]
    ],
    "bandwidth": 24_000_000,
    "id": "SENNHEISER_XSW_D"
  },
  {
    "name": "Sennheiser XSW E",
    "ranges": [
      [821_000_000, 832_000_000],
      [863_000_000, 865_000_000]
    ],
    "bandwidth": 24_000_000,
    "id": "SENNHEISER_XSW_E"
  },
  {
    "name": "Sennheiser XSW K",
    "ranges": [
      [925_000_000, 937_500_000],
    ],
    "bandwidth": 24_000_000,
    "id": "SENNHEISER_XSW_K"
  }
];
export default {
  name: "Coordination_Page",
  mounted() {

  },
  data: () => {
    return {
      chart: null,
      catalogue: catalogue,
      fleet: [],
      frequencyScan: [],
      lowestFreq: 999_999_999_999_999,
      highestFreq: 0,
      fleetIDs: [],
      dialogue_page: 0,
      scan: {
        usedReceivers: [],
        mode: "",
        min_freq: 999_999_999_999_999,
        max_freq: 0,
        usedReceiversIDs: [],
        range_start: 0,
        range_end: 999_999_999_999_999,
        range_step: 25000,
        loading: false,
        receiversByTX: []
      },
      timers: {}
    }
  },
  methods: {
    update_scan_params: function () {
      let ids = [];
      let min_freq = 999_999_999_999_999;
      let max_freq = 0;
      let receivers = [];
      for (const k in this.scan.usedReceivers) {
        const v = this.scan.usedReceivers[k];
        if (v) {
          ids.push(k);
          receivers[k] = this.get_receiver(this.$root.$data.transmitters[this.$root.$data.transmitterIndexes[k]]);
          min_freq = Math.min(min_freq, receivers[k].frequencyRanges[0].startFrequency);
          max_freq = Math.max(max_freq, receivers[k].frequencyRanges[0].endFrequency);
        }
      }

      this.scan.usedReceiversIDs = ids;
      this.scan.receiversByTX = receivers;
      this.scan.max_freq = max_freq;
      this.scan.min_freq = min_freq;
      this.scan.range_start = Math.min(Math.max(this.scan.range_start, min_freq), max_freq)
      this.scan.range_end = Math.max(Math.min(this.scan.range_end, max_freq), min_freq)
    },
    get_receiver: function (transmitter) {
      return this.$root.$data.receivers[this.$root.$data.receiverIndexes[transmitter.receiverID]]
    },
    fetch_scan: function (i) {
      fetch("https://localhost:7221/rfScan/777995160" + (i === 0 ? "?minFreq=578000000&maxFreq=638000000&stepSize=25000" : "")).then(
          (response) => {
            response.json().then((data) => {
              if (data.samples.length < 1 && i < 100)
                setTimeout(() => {
                  this.fetch_scan(i + 1)
                }, 1000);
              else {
                this.frequencyScan = data.samples.map((a) => {
                  this.lowestFreq = Math.min(this.lowestFreq, a.frequency)
                  this.highestFreq = Math.max(this.highestFreq, a.frequency)
                  return {x: a.frequency, y: Math.pow(10, (a.strength / 10))}
                });
                this.render_chart();
              }
            })
          }
      )
    },
    add_to_fleet: function (uid, meta_data) {
      this.fleet.push({
        managed: (uid !== ''),
        locked: false,
        frequency: meta_data.frequency,
        name: meta_data.name,
        type: meta_data.type,
        id: uid
      });
      this.lowestFreq = Math.min(this.lowestFreq, meta_data.frequency)
      this.highestFreq = Math.max(this.highestFreq, meta_data.frequency)
      if (uid !== '')
        this.fleetIDs.push(uid)

      this.render_chart();
    },
    render_chart: function () {
      if (this.$data.chart !== null) {
        this.$data.chart.destroy();
      }

      this.$data.chart = new ChartJS(this.$refs.spectrum, {
        data: {
          datasets: [
            {
              type: 'line',
              label: 'Frequency Scan',
              data: this.frequencyScan,
              fill: {
                target: {value: Math.pow(10, (-110 / 10))},
                above: "#E0A100"
              },

            }],
          mics: this.fleet
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
              max: this.highestFreq,
              min: this.lowestFreq,
              ticks: {
                color: '#CDD1DE',
                callback: function (value, index, values) {
                  // Convert the logarithmic value back to dBm for display
                  return formatter.format(value / 1_000_000) + ' MHz';
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
    },
    start_scan: function () {
      this.scan.loading = true
      let scanners = [];
      this.scan.usedReceiversIDs.sort((a, b) => {
        return this.scan.receiversByTX[a].frequencyRanges[0].startFrequency -
            this.scan.receiversByTX[b].frequencyRanges[0].startFrequency
      });

      let current_freq = this.scan.range_start;
      const end_freq = this.scan.range_end;
      let usedRX = [];

      while (current_freq < this.scan.range_end) {
        let overlapping_receivers = [];
        for (const receiver of this.scan.usedReceiversIDs) {
          let r_start = this.scan.receiversByTX[receiver].frequencyRanges[0].startFrequency
          let r_end = this.scan.receiversByTX[receiver].frequencyRanges[0].endFrequency
          if (r_start <= current_freq && current_freq <= r_end)
            overlapping_receivers.push({start: r_start, end: r_end, receiver})
        }

        if (overlapping_receivers.length === 0) {
          const last_freq = current_freq;
          for (const receiver of this.scan.usedReceiversIDs) {
            if (usedRX.indexOf(receiver) === -1) {
              current_freq = this.scan.receiversByTX[receiver].frequencyRanges[0].startFrequency;
              break;
            }
          }
          if (current_freq !== last_freq)
            continue;
          else
            break;
        }
        const num_receivers = overlapping_receivers.length;
        const lowest_end_freq = overlapping_receivers.reduce((a, b) => Math.min(a, b.end), 999_999_999_999_999);
        const range_end = Math.min(end_freq, lowest_end_freq);
        if (this.scan.mode === "speed") {// Spread all receivers to cover the least possible range each
          const range_length = range_end - current_freq + this.scan.range_step;
          const range_per_receiver = Math.floor(range_length / num_receivers);
          for (let j = 0; j < num_receivers; j++) {
            let scan_end;
            if (j === num_receivers - 1)
              scan_end = range_end;
            else
              scan_end = current_freq + range_per_receiver - this.scan.range_step;

            usedRX.push(overlapping_receivers[j].receiver);
            scanners.push({
              id: overlapping_receivers[j].receiver, start: current_freq, end: scan_end,
              width: scan_end - current_freq
            })
            current_freq = scan_end + this.scan.range_step
            if (current_freq > range_end)
              break

          }
        } else if (this.scan.mode === "quality") {// Spread all receivers to overlap the most possible
          for (let j = 0; j < num_receivers; j++) {
            usedRX.push(overlapping_receivers[j].receiver);
            scanners.push({
              id: overlapping_receivers[j].receiver, start: current_freq, end: range_end,
              width: range_end - current_freq
            })
          }
          current_freq = range_end + this.scan.range_step
        }
      }
      this.frequencyScan = [];
      for (let scanner of scanners) {
        fetch(this.$root.$data._endpoint + '/rfScan/' + scanner.id +
            `?minFreq=${scanner.start}&maxFreq=${scanner.end}&stepSize=${this.scan.range_step}`).then(()=>{
              if (this.$data.timers[scanner.id])
                clearInterval(this.$data.timers[scanner.id]);
              else {
                this.$data.timers[scanner.id] = setInterval(()=>{
                  fetch(this.$root.$data._endpoint + '/rfScan/' + scanner.id).then((d)=>{
                    d.json().then((data)=>{
                      if (data.state === 'Completed') {
                        // Process Scan Data
                        clearInterval(this.$data.timers[scanner.id]);
                      }
                    })
                  })
                }, 10000);
              }
        })
      }
    }
  }
}
</script>

<template>
  <div class="page-container">
    <canvas ref="spectrum"></canvas>
    <button @click="dialogue_page=0;$refs.scanner.showModal();"><span class="material-symbols-outlined">radar</span>
      Scan Frequencies
    </button>
    <button :class="fleet.length === 0 ? 'disabled' : ''" @click=""><span class="material-symbols-outlined">pin</span>
      Calculate Frequencies
    </button>
    <button @click=""><span class="material-symbols-outlined">deployed_code_update</span> Deploy Changes</button>
    <div class="lower-zone">
      <div class="fleet">
        <h2><span class="material-symbols-outlined">dns</span> Wireless Fleet</h2>
        <ul>
          <li v-for="transmitter in fleet" :class="transmitter.locked?'lock':'lock_open'" :data-id="transmitter.uid">
            <a href="#" @click="fleet = fleet.filter((el)=>{return el!==transmitter});render_chart()"><span
                class="material-symbols-outlined">close</span></a>
            <a href="#" @click="transmitter.locked = !transmitter.locked"><span
                class="material-symbols-outlined">{{ transmitter.locked ? 'lock' : 'lock_open' }}</span></a>
            <a v-if="!transmitter.managed" href="#"><span class="material-symbols-outlined">warning</span></a>
            <input v-model="transmitter.name" placeholder="Transmitter Name" type="text">
            <input v-model="transmitter.frequency" placeholder="000.00 MHz" type="text">
          </li>
        </ul>
      </div>
      <div class="network">
        <h2><span class="material-symbols-outlined">lan</span>Network Devices</h2>
        <ul>
          <li v-for="transmitter in $root.$data.transmitters"
              class="inventory-item"
              @click="add_to_fleet(transmitter.uid, {name:transmitter.name, type: 'SHURE_J5E', frequency: transmitter.frequency})">
            <a href="#"><span class="material-symbols-outlined">add</span></a>
            {{ transmitter.name }} <code>{{ transmitter.frequency / 1000000 }} MHz</code>
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
          <li v-for="i in catalogue" class="inventory-item"
              @click="add_to_fleet('', {name:i.name, type: i.id, frequency: i.ranges[0][0]})">
            <a href="#"><span class="material-symbols-outlined">add</span></a>
            {{ i.name }}
          </li>
        </ul>
      </div>
    </div>
    <dialog ref="scanner">
      <a class="close" @click="$refs.scanner.close()"><span class="material-symbols-outlined">close</span></a>
      <h1>Frequency Scan</h1>
      <div v-if="dialogue_page===0">
        <sub>Please select receivers to perform scan with.</sub>
        <ul class="receiver-list">
          <li v-for="transmitter in $root.$data.transmitters"
              class="inventory-item freq-item"
              @click="scan.usedReceivers[transmitter.uid] = !scan.usedReceivers[transmitter.uid]; update_scan_params()">
            <div>
              <transition name="check">
                <span v-if="!scan.usedReceivers[transmitter.uid]" class="material-symbols-outlined">check_box_outline_blank</span>
                <span v-else class="material-symbols-outlined">check_box</span>
              </transition>
            </div>
            <div>{{ transmitter.name }}</div>
            <div>{{ get_receiver(transmitter).manufacturer }} {{ get_receiver(transmitter).modelName }}
              {{ get_receiver(transmitter).freqBand }}
            </div>
          </li>
        </ul>
      </div>
      <div v-if="dialogue_page===1">
        <sub>Scan Parameters</sub>
        <div>
          <label for="startFreq">Start Frequency</label>
          <input id="startFreq" v-model="scan.range_start" :max="scan.max_freq" :min="scan.min_freq"
                 :step="scan.range_step"
                 type="number">
        </div>
        <div>
          <label for="endFreq">End Frequency</label>
          <input id="endFreq" v-model="scan.range_end" :max="scan.max_freq" :min="scan.min_freq" :step="scan.range_step"
                 type="number">
        </div>
        <div>
          <label for="stepSize">Step Size</label>
          <input id="stepSize" v-model="scan.range_step" type="number">
        </div>
        <div>
          <label for="endFreq">Optimisation</label>
          <select v-model="scan.mode">
            <option selected value="speed">Scan Speed</option>
            <option value="quality">Scan Quality</option>
          </select>
        </div>
      </div>
      <div v-if="dialogue_page===2">
        <div v-if="scan.loading" class="loader">
          <span class="material-symbols-outlined">radar</span>
          <h3>Scanning...</h3>
        </div>
        <div v-else>
          <ul>
            <li>{{ scan.usedReceiversIDs.length }} receiver(s)</li>
            <li>Range: {{ scan.range_start }}-{{ scan.range_end }}</li>
            <li>Step Size: {{ scan.range_step }}</li>
            <li>Optimisation: {{ scan.mode }}</li>
          </ul>
        </div>
      </div>
      <button v-if="dialogue_page === 2 && !scan.loading" @click="start_scan">Start Scan <span
          class="material-symbols-outlined">radar</span></button>
      <button v-if="dialogue_page < 2" @click="dialogue_page++">Continue <span class="material-symbols-outlined">arrow_right_alt</span>
      </button>
    </dialog>
  </div>
</template>

<style scoped>
dialog {
  position: absolute;
  top: 10vh;
  max-height: 70vh;
  min-height: 50vh;
  overflow-y: auto;
  width: 40vw;
}

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
  gap: 0.5em;
  margin-top: 1em;

  & > * {
    border: var(--text-200) 2px solid;
    border-radius: 0.8em;
    padding: 1em;
    overflow: hidden;

    & > ul {
      overflow-y: auto;
    }

    & > h2 {
      text-align: left;
      font-size: medium;
      display: inline-flex;
      margin: 0;
      gap: 0.5em;

      & > span {
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

  & > a {
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

  & > li {
    display: flex;
    gap: 0.5em;
    align-items: baseline;
    transition: opacity 250ms ease, filter 250ms ease;

    &.lock {
      opacity: .7;
      filter: grayscale(1);

      & > input {
        pointer-events: none;
      }
    }

    & > a {
      align-self: center;
    }
  }
}

.freq-item {
  display: grid;
  grid-template-columns: 36pt auto;
  grid-template-rows: 24pt 12pt;
  height: 36pt;
  gap: 0;
  align-items: center;
  transform: scale(1) !important;

  & > div:first-of-type {
    grid-column: 1/2;
    grid-row: 1/3;
    position: relative;
    width: 30pt;
    height: 30pt;
    justify-self: center;

    & > span {
      position: absolute;
      top: 0;
      left: 0;
      overflow: hidden;
      width: 100%;
      font-size: 30pt;
    }
  }

  & > div {
    text-align: left;
    grid-column: 2/3;

    &:last-of-type {
      align-self: end;
      opacity: 0.6;
      font-size: 0.9em;
    }
  }
}

.close {
  position: absolute;
  top: 1em;
  right: 1em;
  cursor: pointer;
}

.disabled {
  pointer-events: none;
  opacity: 0.6;
}

.receiver-list {
  max-height: min(calc(36pt * 8), 30vh);
  overflow-y: auto;
}

dialog > div {
  margin-bottom: 1em;
  display: flex;
  flex-direction: column;
  justify-content: start;
  text-align: left;

  & > sub {
    text-align: center;
  }

  & > div:has(input), & > div:has(select) {
    display: grid;
    grid-template-columns: 1fr 4fr;
    margin: 0.5em 0;
  }
}

.loader {
  text-align: center;
  display: flex;
  flex-direction: column;
  min-height: 30vh;
  justify-content: center;
  overflow: hidden;
  max-height: 30vh;

  & > .material-symbols-outlined {
    font-size: 36pt;
    color: var(--primary-500);
    animation: spin 1.5s infinite linear;
  }
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  50% {
    transform: rotate(180deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>