<script>
export default {
  name: "Monitor_Page"
}
</script>

<template>
<div class="page-container">
  <div class="transmitters">
    <div class="transmitter" v-for="i in 30">
      <span>{{i.toString().padStart(2,'0')}}</span>
      <h1>John Smith</h1>
      <div class="level AF"><span class="material-symbols-outlined">graphic_eq</span> <span class="bullet" v-for="j in 8"></span></div>
      <div class="level RF"><span class="material-symbols-outlined">signal_cellular_alt</span> <span class="bullet" v-for="j in 8"></span></div>
      <div class="level RF"><span class="material-symbols-outlined">signal_cellular_alt</span> <span class="bullet" v-for="j in 8"></span></div>
      <div class="battery"><span class="material-symbols-outlined">battery_5_bar</span> 70%</div>
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
        "a b c"
        "a d c"
        "a e c"
        "f h c";
  &>h1 {
    font-size: 1em;
    margin: 0;
    line-height: 1.5em;
    text-align: left;
    grid-area: b;
  }
  &>span:first-of-type {
    font-weight: bold;
    font-size: 2em;
    grid-area: a;
  }
  &>.level:nth-of-type(1) {
    grid-area: d;
  }
  &>.level:nth-of-type(2) {
    grid-area: e;
  }
  &>.level:nth-of-type(3) {
    grid-area: h;
  }
  &>.level {
    height: 1em;
    margin-bottom: 0.5em;
    display: flex;
    gap: 0.25em;
    align-items: center;
    &>.bullet {
      display: block;
      width: 12pt;
      height: 12pt;
      background-color: var(--primary-300);
      border-radius: 50%;
    }
  }
  &>.battery {
    grid-area: f;
    display: flex;
    align-items: center;
    font-size: 0.8em;
    justify-content: center;
    &>*{
      font-size: 16px;
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
  gap: 1em;
  grid-template-columns: auto auto auto auto auto;
  justify-content: space-evenly;
  overflow: hidden;
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
}
</style>