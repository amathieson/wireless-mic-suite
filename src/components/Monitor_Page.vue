<script>
export default {
  name: "Monitor_Page",
  methods: {
    computeName(name) {
      if (name)
        return [name.substring(0, 2).toUpperCase(), name];
      else return ["", ""];
    },
    computeBattery(level) {
      const levels = ['battery_full_alt', 'battery_horiz_075', 'battery_horiz_050', 'battery_low', 'battery_horiz_000']
      return levels[Math.round((1 - level) * (levels.length - 1))];
    }
  },
  emits: ['config']
}
</script>

<template>
  <div class="page-container">
    <div class="transmitters">
      <div class="transmitter" v-for="transmitter in $root.$data.transmitters"
        @click="$emit('config', 'TRANSMITTER', transmitter.uid);"
        :class="{ 'online': transmitter.transmitterType !== 'UNKNOWN' && transmitter.transmitterType !== null }">
        <h1>{{ computeName(transmitter.name)[1] }}</h1>
        <div class="level AF"><span class="material-symbols-outlined">graphic_eq</span> <span class="bullet"
            v-for="j in 10" :data-active="j <= (transmitter.lastMeterData?.audioLevel * 10)"></span></div>
        <div class="level RF"><span class="material-symbols-outlined">signal_cellular_alt</span> <span class="bullet"
            v-for="j in 10"
            :data-active="j <= (transmitter.lastMeterData?.rssiA * 7) || (j === 10 && (transmitter.lastMeterData?.diversity & 1) === 1)"></span>
        </div>
        <div class="level RF"><span class="material-symbols-outlined">signal_cellular_alt</span> <span class="bullet"
            v-for="j in 10"
            :data-active="j <= (transmitter.lastMeterData?.rssiB * 7) || (j === 10 && (transmitter.lastMeterData?.diversity & 2) === 2)"></span>
        </div>
        <div class="right-col">
          <span>{{ computeName(transmitter.name)[0] }}</span>
          <div class="battery">
            <div class="material-symbols-outlined">{{ computeBattery(transmitter.batteryLevel) }}</div>
            <div class="battery-percent">{{ (transmitter.batteryLevel * 100).toFixed(0) }}%</div>
          </div>
        </div>
      </div>
    </div>
    <div v-if="!$root.$data.connected" class="error-container">
      <div class="error-message">Could not establish a connection to the Wireless Mic Suite server</div>
    </div>
    <div v-if="$root.$data.transmitters.length == 0" class="error-container">
      <div class="error-message">No wireless mics detected</div>
    </div>
  </div>
</template>

<style scoped>
.transmitter {
  opacity: 0.5;
  background-color: #80808018;
  padding: 0.25em 0.5em;
  max-height: 8em;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr;
  align-items: center;
  border-radius: 0.8em;
  border: 2px solid var(--text-200);
  gap: 0.25em 0.5em;
  grid-template-areas:
    "b b b"
    "a d c"
    "a e c"
    "a h c";

  &>h1 {
    font-size: 1em;
    margin: 0;
    line-height: 1.5em;
    text-align: center;
    grid-area: b;
    border-bottom: 1px solid var(--text-200);
  }

  &>.level:nth-of-type(1) {
    grid-area: d;
    --bullet-colour: #51ff17;

    &>.bullet:last-of-type {
      --bullet-colour: #ff1717;
    }
  }

  &>.level:nth-of-type(2) {
    grid-area: e;
  }

  &>.level:nth-of-type(3) {
    grid-area: h;
  }

  &>.level:nth-of-type(3),
  &>.level:nth-of-type(2) {
    --bullet-colour: #ff5b17;

    &>.bullet:nth-last-of-type(3) {
      --bullet-colour: #ff1717;
    }

    &>.bullet:last-of-type {
      --bullet-colour: #173eff;
    }
  }

  &>.level {
    height: 1em;
    display: flex;
    gap: 0.25em;
    align-items: center;

    &>.material-symbols-outlined {
      font-size: 14pt;
    }

    &>.bullet {
      display: block;
      width: 10pt;
      height: 10pt;
      background-color: var(--dark-200);
      border-radius: 50%;

      &[data-active=true] {
        background-color: var(--bullet-colour);
      }

      &:nth-last-child(2) {
        background-color: transparent
      }
    }
  }

  &>.right-col {
    grid-area: a;

    &>span:first-of-type {
      font-weight: bold;
      font-size: 2em;
    }

    &>.battery {
      display: flex;
      align-items: center;
      font-size: 0.8em;
      justify-content: center;
      flex-direction: column;

      &>* {
        font-size: 28px;
        margin: -2px;
      }

      &>.battery-percent {
        font-size: 14px;
        font-weight: 600;
        margin-bottom: 2px;
      }
    }
  }

  &>img {
    grid-area: c;
    width: 100%;
  }
}

.transmitter:hover {
  background-color: #80808030;
}

.transmitter:active {
  background-color: #80808000;
}

.online {
  opacity: 1;
}

.error-container {
  float: left;
  width: 40vw;
  background-color: var(--dark-200);
}

.error-message {
  color: var(--text-200);
  opacity: 50%;
  font-size: 1.5rem;
  font-weight: 600;
  margin: 20px;
}

.transmitters {
  float: left;
  display: grid;
  gap: 0.7em;
  grid-template-columns: auto auto auto auto auto;
  justify-content: space-evenly;
}

@media screen and (width < 1500px) {
  .transmitters {
    grid-template-columns: auto auto auto auto;
  }
}

@media screen and (width < 1300px) {
  .transmitters {
    grid-template-columns: auto auto auto;
  }
}

@media screen and (width < 900px) {
  .transmitters {
    grid-template-columns: auto auto;
  }
}

@media screen and (width < 700px) {
  .transmitters {
    grid-template-columns: auto;
  }
}

.page-container {
  display: flex;
  gap: 1em;
  justify-content: center;
}
</style>