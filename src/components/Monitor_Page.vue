<script>
export default {
  name: "Monitor_Page",
  methods: {
    computeName(name) {
        return [name.substring(0, 2).toUpperCase(), name];
    },
    computeBattery(level) {
      const levels = ['battery_full_alt', 'battery_horiz_075', 'battery_horiz_050', 'battery_low', 'battery_horiz_000']
      return levels[Math.round((1-level) * (levels.length-1))];
    }
  }
}
</script>

<template>
<div class="page-container">
  <div class="transmitters">
    <div class="transmitter" v-for="transmitter in $root.$data.transmitters">
      <span>{{computeName(transmitter.name)[0]}}</span>
      <h1>{{computeName(transmitter.name)[1]}}</h1>
      <div class="level AF"><span class="material-symbols-outlined">graphic_eq</span> <span class="bullet" v-for="j in 10" :data-active="j <= (transmitter.lastMeterData.AudioLevel * 10)"></span></div>
      <div class="level RF"><span class="material-symbols-outlined">signal_cellular_alt</span> <span class="bullet" v-for="j in 10" :data-active="j <= (transmitter.lastMeterData.RssiA * 7) || j===10"></span></div>
      <div class="level RF"><span class="material-symbols-outlined">signal_cellular_alt</span> <span class="bullet" v-for="j in 10" :data-active="j <= (transmitter.lastMeterData.RssiB * 7) || j===10"></span></div>
      <div class="battery"><span class="material-symbols-outlined">{{computeBattery(transmitter.batteryLevel)}}</span></div>
    </div>
  </div>
<!--  <div class="error-container">-->
<!--  </div>-->
</div>
</template>

<style scoped>
.transmitter {
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
        "f h c";
  &>h1 {
    font-size: 1em;
    margin: 0;
    line-height: 1.5em;
    text-align: center;
    grid-area: b;
    border-bottom: 1px solid var(--text-200);
  }
  &>span:first-of-type {
    font-weight: bold;
    font-size: 2em;
    grid-area: a;
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
  &>.level:nth-of-type(3), &>.level:nth-of-type(2) {
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
  &>.battery {
    grid-area: f;
    display: flex;
    align-items: center;
    font-size: 0.8em;
    justify-content: center;
    &>*{
      font-size: 28px;
    }
  }
  &>img {
    grid-area: c;
    width: 100%;
  }
}
.error-container {
  float: right;
  width: 20vw;
  background-color: var(--text-200);
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