<script>
export default {
  name: "System_Page",
  data: ()=>{
    return {
      network: {}
    }
  },
  emits: ['config'],
  mounted() {
    this.updateNetwork();
  },
  methods: {
    updateNetwork(){
      let network = {};

      this.$root.$data.receivers.forEach((rx) => {
        const key = rx.modelName + rx.manufacturer + rx.freqBand;
        if (network[key] === undefined) {
          network[key] = {
            modelName: rx.modelName,
            manufacturer: rx.manufacturer,
            freqBand: rx.freqBand,
            receivers:[]
          }
        }
        let transmitters = [];
        rx.wirelessMicIDs.forEach((tx)=>{
          let txData = this.$root.$data.transmitters[this.$root.$data.transmitterIndexes[tx]]
          transmitters.push({
            name: txData.name,
            type: txData.transmitterType,
            uid: txData.uid,
            frequency: txData.frequency
          })
        })
        network[key].receivers.push({
          uid: rx.uid,
          name: transmitters.map((e)=>e.name).join(' - '),
          transmitters
        });
      })

      this.network = network;
    }
  }
}
</script>

<template>
<div class="page-container">
    <details open v-for="node in network">
      <summary class="category">{{node.manufacturer}} {{node.modelName}} {{node.freqBand}}</summary>
        <details v-for="receiver in node.receivers">
          <summary class="receiver">{{receiver.name}}<button @click="$emit('config', 'RECEIVER', receiver.uid);"><span class="material-symbols-outlined">settings</span></button></summary>
          <ul>
            <li v-for="transmitter in receiver.transmitters" class="transmitter">{{ transmitter.type }} - {{transmitter.name}} <code>{{((transmitter.frequency)/1000000).toFixed(3)}} MHz</code><button @click="$emit('config', 'TRANSMITTER', transmitter.uid);"><span class="material-symbols-outlined">settings</span></button></li>
          </ul>
        </details>
    </details>
</div>
</template>

<style scoped>
button {
  background: none;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  padding: 24px;
  display: flex;
  justify-content: center;
  margin: 0 0 0 auto;

  &>* {
    display: block;
    font-size: 24px;
  }

}
.page-container {
  display: block;
  text-align: left;
}
summary {
  background: var(--dark-500);
  padding: 0.5em 1em;
  font-weight: 800;
  border-bottom: 2px solid var(--text-200);
  transition: background 250ms ease;
  display: flex;
  gap: 0.5em;
  cursor: pointer;
  align-items: center;
}
summary:hover {
  background: var(--dark-200);
}
.receiver {
  margin-left: 2em;
  list-style: none;
}
.transmitter {
  display: flex;
  padding: 0.5em 1em;
  border-bottom: 2px solid var(--text-200);
  gap: 0.5em;
  background: var(--dark-500);
  transition: background 250ms ease;
  align-items: center;
}
.transmitter > code {
  align-self: center;
  font-size: 12pt;
  font-weight: 300;
  color: var(--text-200);
}
.transmitter:last-of-type, details:last-of-type>summary {
  border-bottom: none;
}
details[open]:last-of-type>summary {
  border-bottom: 2px solid var(--text-200);
}
summary::before, .transmitter::before {
  font-family: 'Material Symbols Outlined', sans-serif;
  content: 'arrow_right';
}
[open] > summary::before {
  font-family: 'Material Symbols Outlined', sans-serif;
  content: 'arrow_drop_down';
}
.receiver::before {
  content: 'dns' !important;
}
.transmitter::before {
  content: 'mic' !important;
}
ul {
  margin: 0.5em 0 0.5em 1em;
}
</style>