<script setup>
import { vOnLongPress } from '@vueuse/components'
</script>

<script>
export default {
  name: "System_Page",
  data: ()=>{
    return {
      network: {},
      selected: new Set,
      selectionType: "",
      suppressClick: false,
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
          name: transmitters.map((e)=>e.name).join(' – '),
          transmitters
        });
      })

      this.network = network;
    },
    longPress(type, uid) {
      if (this.selectionType === "" || this.selectionType === type) {
        this.selected.add(uid);
        this.selectionType = type;
      }
    },
    clickItem(type, uid) {
      if (this.suppressClick) {
        this.suppressClick = false;
        return;
      }
      if (this.selected.size === 0)
        this.$emit('config', type, uid);
      else {
        if (this.selectionType === type) {
          if (this.selected.has(uid)) {
            this.selected.delete(uid);
            if (this.selected.size === 0)
              this.selectionType = "";
          } else
            this.selected.add(uid);
        }
      }
    }
  }
}
</script>

<template>
<div class="page-container">
    <details open v-for="node in network">
      <summary class="category">{{node.manufacturer}} {{node.modelName}} {{node.freqBand}}</summary>
        <details v-for="receiver in node.receivers">
          <summary v-on-long-press.stop="()=>{suppressClick = true; longPress('RECEIVER', receiver.uid); return false}" :data-selected="selected.has(receiver.uid)" class="receiver" @click="clickItem('RECEIVER', receiver.uid);"><button @click.stop="(e)=>{e.target.parentElement.parentElement.parentElement.toggleAttribute('open')}"><span class="material-symbols-outlined">arrow_right</span></button>{{receiver.name}}</summary>
          <ul>
            <li v-on-long-press.stop="()=>{suppressClick = true; longPress('TRANSMITTER', transmitter.uid); return false}" :data-selected="selected.has(transmitter.uid)" @click="clickItem('TRANSMITTER', transmitter.uid)" v-for="transmitter in receiver.transmitters" class="transmitter">{{ transmitter.type }} &ndash; {{transmitter.name}} <code>{{((transmitter.frequency)/1000000).toFixed(3)}} MHz</code></li>
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
[data-selected]:not([data-selected="false"]) {
  color: var(--primary-300);
}
.page-container {
  display: block;
  text-align: left;
}
summary {
  background: var(--dark-500);
  padding: 0.5em 1em;
  font-weight: 800;
  border-bottom: 1px solid var(--text-200);
  transition: background 250ms ease;
  display: flex;
  gap: 0.5em;
  cursor: pointer;
  align-items: center;
}
summary:hover, .transmitter:hover {
  background: var(--dark-200);
  cursor: pointer;
}
.receiver {
  font-weight: 400;
  margin-left: 2em;
  list-style: none;
}
.transmitter {
  display: flex;
  padding: 0.5em 1em;
  border-bottom: 1px solid var(--text-200);
  gap: 0.75em;
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
  opacity: 0.65;
}
.transmitter::before {
  content: 'mic' !important;
  opacity: 0.65;
}
ul {
  margin: 0.5em 0 0.5em 1em;
}
</style>