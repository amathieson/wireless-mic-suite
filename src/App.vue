<script setup>
import Monitor_Page from "./components/Monitor_Page.vue";
import Navbar from "./Navbar.vue";
import System_Page from "./components/System_Page.vue";
import Coordination_Page from "./components/Coordination_Page.vue";
import Device_Config from "./components/Device_Config.vue";
</script>

<template>
  <Navbar @back="config_active = false" :back="config_active" :active_page="active_page" @navigate="args => active_page = args"/>
  <transition mode="out-in">
  <Monitor_Page v-if="active_page === 'Monitor_Page'"/>
  </transition>
  <transition mode="out-in">
  <System_Page v-if="active_page === 'System_Page'"/>
  </transition>
  <transition mode="out-in">
  <Coordination_Page v-if="active_page === 'Coordination_Page'"/>
  </transition>
  <transition mode="out-in" name="slide-right">
  <Device_Config :active="config_active"/>
  </transition>
</template>
<script>
export default {
  data: ()=>{
    return {
      config_active: false,
      active_page:"Monitor_Page",
      transmitterIndexes:{},
      transmitters:[
        {
          "lastMeterData": {
            "RssiA": 0,
            "RssiB": 0,
            "AudioLevel": 0
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
        }
      ],
      socket: null
    }
  },
  mounted() {
    this.fetchMics();
    // setInterval(()=>{this.fetchMics();}, 10000)
    // Create WebSocket connection.
    this.$data.socket = new WebSocket("wss://localhost:7221/ws");

// Connection opened
    this.$data.socket.addEventListener("open", (event) => {    });

// Listen for messages
    this.$data.socket.addEventListener("message", (event) => {
      const data = JSON.parse(event.data);
      if (data.PropertyName)
        console.log(data);
      else{
        data.forEach(element => {
          this.$data.transmitters[this.$data.transmitterIndexes[element.UID]].lastMeterData
          = element.MeteringData;
        })
      }
    });

  },
  methods: {
    fetchMics: function () {
      fetch("https://localhost:7221/getWirelessMics").then((response) => {
        response.json().then((data) => {
          this.$data.transmitters = data;
          for (const index in data) {
            this.$data.transmitterIndexes[data[index].uid] = index;
          }
        });
      });
    }
  }
}

</script>
