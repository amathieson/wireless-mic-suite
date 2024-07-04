<script>
export default {
  name: "Device_Config",
  props: ["active", "device_type", "id"],
  emits: ['close'],
  data: ()=>{
    return {
      // device_type: "TRANSMITTER", // 'TRANSMITTER' || 'RECEIVER'
      config: {},
      brand: "",
      model: "",
      rxID: ""
    }
  },
  watch: {
    id: function (){
      if (this.device_type === "TRANSMITTER"){
        const txID = this.$root.$data.transmitterIndexes[this.id];
        const txData = this.$root.$data.transmitters[txID];
        const rxID = this.$root.$data.receiverIndexes[txData.receiverID];
        const rxData = this.$root.$data.receivers[rxID];
        this.brand = rxData.manufacturer;
        this.model = txData.transmitterType;
        this.config = txData;
        this.rxID = txData.receiverID;
      }
    }
  },
  methods: {
    identify: function () {
      fetch(this.$root.$data._endpoint + '/identifyWirelessReceiver/' + this.rxID)
    },
    save: function () {
      const params = ["name", "frequency", "group", "channel", "lockMode", "gain", "sensitivity", "outputGain", "mute"];
      params.forEach(param => {
        fetch(this.$root.$data._endpoint + `/setWirelessMic/${this.id}/${param}/${this.config[param]}`).then((response) => {
          response.json().then((data)=>{if(!data.success){console.log(data)}});
        })
      })
    }
  }
}
</script>

<template>
  <div class="cover wide-only" @click="$emit('close')" :active="active"></div>
  <div class="container" :active="active">
    <div class="header" v-if="config.name != null">
      <div class="device-image" :style="device_type === 'RECEIVER' ? '' :
      'background-image: url(\'device_images/' + brand.toUpperCase() + '_' + model.toUpperCase() + '.webp\')'">
        <span v-if="device_type === 'RECEIVER'" class="material-symbols-outlined">dns</span>
        <span v-if="model.toUpperCase() === 'UNKNOWN'" class="material-symbols-outlined">question_mark</span>
      </div>
      <div class="device-info">
        <h1>{{brand}} {{model}} {{config.name}}</h1>
        <code><span class="material-symbols-outlined">tag</span> {{ id }}</code>
        <code v-if="device_type === 'RECEIVER'"><span class="material-symbols-outlined">terminal</span> 1.171</code>
        <code v-else><span class="material-symbols-outlined">dns</span> {{rxID}}</code>
      </div>
    </div>
    <div class="buttons" v-if="config.name != null">
      <button @click="save" class="primary"><span class="material-symbols-outlined">save</span> <span>Save Changes</span></button>
      <button @click="identify"><span class="material-symbols-outlined">point_scan</span> <span>Identify</span></button>
      <button v-if="device_type === 'RECEIVER'"><span class="material-symbols-outlined">restart_alt</span> <span>Reboot</span></button>
    </div>
    <div class="config" v-if="config.name != null">
      <section v-if="device_type === 'RECEIVER'">
        <h2><span class="material-symbols-outlined">router</span> Network</h2>
        <ul class="inputs">
          <li>
            <label for="DHCP">DHCP
              <input type="checkbox" class="switch" id="DHCP" checked><label for="DHCP">DHCP</label>
            </label>
          </li>
          <li>
            <label for="ipaddr">IP Address</label>
            <input placeholder="0.0.0.0" type="text" id="ipaddr">
          </li>
          <li>
            <label for="subnet">Subnet Address</label>
            <input placeholder="0.0.0.0" type="text" id="subnet">
          </li>
          <li>
            <label for="gateway">Gateway Address</label>
            <input placeholder="0.0.0.0" type="text" id="gateway">
          </li>
        </ul>
      </section>
      <section v-if="device_type === 'TRANSMITTER'">
        <h2><span class="material-symbols-outlined">mic</span> Transmitter</h2>
        <ul class="inputs">
          <li>
            <label for="txname">Name</label>
            <input v-model="config.name" placeholder="John Doe" type="text" id="txname">
          </li>
          <li>
            <label for="freq">Frequency</label>
            <span><input v-model="config.frequency" placeholder="000.000" type="text" id="freq"> MHz</span>
          </li>
          <li>
            <label for="group">Group</label>
            <input v-model="config.group" placeholder="1" type="text" id="group">
          </li>
          <li>
            <label for="chan">Channel</label>
            <input v-model="config.channel" placeholder="1" type="text" id="chan">
          </li>
          <li>
            <label for="lock">Lock</label>
            <select v-model="config.lockMode">
              <option>None</option>
              <option>Mute</option>
              <option>Power</option>
              <option>Frequency</option>
              <option>FrequencyPower</option>
              <option>All</option>
            </select>
          </li>
        </ul>
      </section>
      <section v-if="device_type === 'TRANSMITTER'">
        <h2><span class="material-symbols-outlined">equalizer</span> Gain</h2>
        <ul class="inputs">
          <li>
            <label for="gain">Input Gain</label>
            <span><input v-model="config.gain" placeholder="0" type="text" id="gain"> db</span>
          </li>
          <li>
            <label for="sens">Sensitivity</label>
            <span><input v-model="config.sensitivity" placeholder="0" type="text" id="sens"> db</span>
          </li>
          <li>
            <label for="outgain">Output Gain</label>
            <span><input v-model="config.outputGain" placeholder="0" type="text" id="outgain"> db</span>
          </li>
          <li>
            <label for="mute">Mute
              <input v-model="config.mute" type="checkbox" class="switch" id="mute" checked><label for="mute">Mute</label>
            </label>
          </li>
        </ul>
      </section>
    </div>
  </div>
</template>

<style scoped>
.cover[active='false'] {
  opacity: 0;
  pointer-events: none;
}
.cover {
  position: absolute;
  left: 0;
  top: var(--bar-height);
  width: 100%;
  height: calc(100% - var(--bar-height));
  background: black;
  opacity: 0.4;
  cursor: pointer;
  pointer-events: auto;
  transition: opacity 0.3s;
}
.device-image {
  width: 10em;
  height: 10em;
  overflow: hidden;
  background-position: 50% 50%;
  background-size: 200%;
  position: relative;
  z-index: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  &>*{font-size: 8em; color: var(--text-200)}
}
.device-image::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: block;
}

.container[active='false'] {
  right: calc(-40% - 4em - 20px);
  width: 0;
  overflow: hidden;
  margin: 0;
  padding: 0;
}

.container {
  position: absolute;
  top: calc(var(--bar-height) + 1em);
  right: 0;
  width: 40%;
  background-color: var(--dark-500);
  height: calc(100% - var(--bar-height) - 2em);
  padding: 0.5em 2em;
  box-shadow: #18191a 0 0 10px 10px;
  transition: right 500ms ease;
  overflow-y: auto;
}
.header {
  display: flex;
  gap: 1em;
  border-bottom: 1px solid var(--primary-500);
}
.device-info {
  display: flex;
  flex-direction: column;
  margin-top: auto;
  margin-bottom: 1em;
  &>h1 {
    font-size: 16pt;
    margin: 0 0 0.5em;
  }
  &>code {
    display: inline-flex;
    align-items: center;
    &>span {
      color: var(--text-200);
      margin-right: 0.25em;
    }
  }
}
.buttons {
  margin-top: 1em;
}
section > h2 {
  display: flex;
  align-items: center;
  gap: 0.5em;
  &>.material-symbols-outlined{
    font-size: 30px;
    color: var(--primary-500);
  }
}
.inputs {
  display: flex;
  flex-direction: column;
  list-style: none;
  margin: 0;
  &>li {
    display: grid;
    margin-bottom: 0.5em;
    &>label {
      color: var(--text-200);
    }
  }
}


label{
  &>input[type=checkbox]{
    height: 0;
    width: 0;
    visibility: hidden;
  }

  &>label {
    cursor: pointer;
    text-indent: -9999px;
    width: 2.1em;
    height: 1.1em;
    background: var(--dark-200);
    display: inline;
    position: relative;
    transition: background 250ms ease;
    color:transparent;
  }

  &>label:after {
    content: '';
    position: absolute;
    top: 0.1em;
    left: 0.1em;
    width: 1em;
    height: 1em;
    background: var(--text-500);
    transition: 0.3s;
    transform: scale(0.8);
  }

  &>input:checked + label {
    background: var(--primary-500);
  }
  &>input:checked + label:after {
    background: var(--dark-200);
  }

  &>input:checked + label:after {
    left: calc(100% - 0.1em);
    transform: translateX(-100%) scale(0.8);
  }

  &>label:active:after {
    width: 1.3em;
  }
}
label > label {
  user-select: none;
}
li > span {
  width: 100%;
  display: flex;
  align-items: center;
  gap: 1em;
}
@media screen and (width < 1024px) {
  .container {
    position: absolute;
    top: calc(var(--bar-height) + 2px);
    right: 1em;
    width: calc(100% - 2em);
    height: calc(100% - var(--bar-height) - 2em);
    padding: 0;
    box-shadow: none;
  }
  .container[active='false'] {
    right: -100%;
  }
  button>span {
    display: none;
  }
  button>span.material-symbols-outlined {
    display: unset;
  }
}
</style>