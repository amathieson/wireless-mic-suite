<script setup>
import Monitor_Page from "./components/Monitor_Page.vue";
import Navbar from "./components/Navbar.vue";
import System_Page from "./components/System_Page.vue";
import Coordination_Page from "./components/Coordination_Page.vue";
import Device_Config from "./components/Device_Config.vue";
</script>

<template>
  <Navbar :active_page="active_page" :back="config_active" @back="config_active = false"
    @navigate="args => active_page = args" />
<!--  <transition mode="out-in">-->
    <Monitor_Page v-if="active_page === 'Monitor_Page'" @config="config" />
<!--  </transition>-->
<!--  <transition mode="out-in">-->
    <System_Page v-if="active_page === 'System_Page'" ref="coord" @config="config" />
<!--  </transition>-->
<!--  <transition mode="out-in">-->
    <Coordination_Page v-if="active_page === 'Coordination_Page'" />
<!--  </transition>-->
  <transition mode="out-in" name="slide-right">
    <Device_Config :id="device_uid" :active="config_active" :device_type="device_type"
      @close="config_active = false; device_uid = ''; device_type = null" />
  </transition>
</template>
<script>
import {ref} from "vue";

let active_page = ref("Monitor_Page");
let config_active = ref(false);
export default {
  data: () => {
    return {
      _endpoint: "https://localhost:7221",//"https://localhost:7221",
      _ws_endpoint: "wss://localhost:7221/ws",//"wss://localhost:7221/ws",
      transmitterIndexes: {},
      transmitters: [],
      socket: null,
      receivers: [],
      receiverIndexes: {},
      device_type: null,
      device_uid: "",
      connected: false,
    }
  },
  mounted() {
    this.fetchMics();
    this.fetchReceivers();
    setInterval(() => {
        this.fetchMics();
    }, 10000)
    // Create WebSocket connection.
    this.$data.socket = new WebSocket(this.$data._ws_endpoint);

    // Connection opened
    this.$data.socket.addEventListener("open", (event) => {
    });

    // Listen for messages
    this.$data.socket.addEventListener("message", (event) => {
      const data = JSON.parse(event.data);
      if (data.propertyName) {
        if (!this.$data.transmitterIndexes[data.uid]) {
          this.$data.transmitters[this.$data.transmitterIndexes[data.uid]] = {};
        }
        this.$data.transmitters[this.$data.transmitterIndexes[data.uid]][data.propertyName] = data.value;
        // if (this.$refs.coord)
          // this.$refs.coord.updateNetwork();
      } else {
        data.forEach(element => {
          if (this.$data.transmitterIndexes[element.uid])
            this.$data.transmitters[this.$data.transmitterIndexes[element.uid]].lastMeterData
              = element.meteringData;
          else {
            console.log("Unknown Transmitter: ", element.uid);
          }
        })
      }
    });

  },
  methods: {
    config: function (device_type, uid) {
      config_active.value = true;
      this.device_uid = uid;
      this.device_type = device_type;
      history.pushState("config", "");
    },
    fetchMics: function () {
      fetch(this.$data._endpoint + "/getWirelessMics").catch((ex) => {
        console.log(`Failed to fetch wireless mics: ${ex}`);
        this.$data.connected = false;
      }).then((response) => {
        this.$data.connected = true;
        response.json().then((data) => {
          this.$data.transmitters = data;
          for (const index in data) {
            this.$data.transmitterIndexes[data[index].uid] = index;
          }
          if (this.$refs.coord)
            this.$refs.coord.updateNetwork();
        });
      });
    },
    fetchReceivers: function () {
      fetch(this.$data._endpoint + "/getWirelessReceivers").catch((ex) => {
        console.log(`Failed to fetch wireless receivers: ${ex}`);
        this.$data.connected = false;
      }).then((response) => {
        this.$data.connected = true;
        response.json().then((data) => {
          this.$data.receivers = data;
          for (const index in data) {
            this.$data.receiverIndexes[data[index].uid] = index;
          }
          if (this.$refs.coord)
            this.$refs.coord.updateNetwork();
        });
      });
    }
  },
  watch: {
    active_page: function (val) {
      history.pushState(val, "");
    }
  }
}
history.pushState("Monitor_Page", "");
addEventListener("popstate", (event) => {
  if (event.state) {
    active_page.value = event.state;
    config_active.value = false;
  }
})

</script>
