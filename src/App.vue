<script setup>
import Monitor_Page from "./components/Monitor_Page.vue";
import Navbar from "./Navbar.vue";
import System_Page from "./components/System_Page.vue";
import Coordination_Page from "./components/Coordination_Page.vue";
</script>

<template>
  <Navbar :active_page="active_page" @navigate="args => active_page = args"/>
  <transition mode="out-in">
  <Monitor_Page v-if="active_page === 'Monitor_Page'"/>
  </transition>
  <transition mode="out-in">
  <System_Page v-if="active_page === 'System_Page'"/>
  </transition>
  <transition mode="out-in">
  <Coordination_Page v-if="active_page === 'Coordination_Page'"/>
  </transition>
</template>
<script>
export default {
  data: ()=>{
    return {
      active_page:"Monitor_Page",
      transmitters:[
        {
          "lastMeterData": {
            "rssiA": 0,
            "rssiB": 0,
            "audioLevel": 0
          },
          "receiverID": 464268611,
          "uid": 1507441391,
          "name": "01_Rachel",
          "gain": 10,
          "outputGain": 0,
          "mute": false,
          "frequency": 610100000,
          "group": null,
          "channel": null,
          "transmitterType": "UNKNOWN",
          "batteryLevel": 0
        },
        {
          "lastMeterData": {
            "rssiA": 0.38,
            "rssiB": 1,
            "audioLevel": 0
          },
          "receiverID": 464268611,
          "uid": 1507441392,
          "name": "02_Steph",
          "gain": 10,
          "outputGain": 0,
          "mute": false,
          "frequency": 578500000,
          "group": 2,
          "channel": 0,
          "transmitterType": "UR1",
          "batteryLevel": 1
        }
      ]
    }
  },
  mounted() {
    // setInterval(()=>{
    //   fetch("https://localhost:7221/getWirelessMics").then((response)=>{response.json().then((data)=>{
    //     this.$data.transmitters = data;
    //   })})
    // }, 10)
  }
}
</script>
