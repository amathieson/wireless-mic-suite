<template>
  <div class="nav-bar">
    <img class="wide-only" src="/GAOS-logo-light-small.png" alt="GAOS">
    <img class="narrow-only" v-if="!$props.back" src="/GAOS-logo-light-small.png" alt="GAOS">
    <a class="narrow-only" v-else @click="$emit('back')"><span class="material-symbols-outlined">arrow_back</span></a>
    <h1 class="narrow-only">{{$props.active_page.split("_")[0]}}</h1>
    <a class="wide-only" :data-active="$props.active_page==='Monitor_Page'" @click="$emit('navigate', 'Monitor_Page')"><span class="material-symbols-outlined">monitoring</span> Monitor</a>
    <a class="wide-only" :data-active="$props.active_page==='System_Page'" @click="$emit('navigate', 'System_Page')"><span class="material-symbols-outlined">dns</span> Wireless System</a>
    <a class="wide-only" :data-active="$props.active_page==='Coordination_Page'" @click="$emit('navigate', 'Coordination_Page')"><span class="material-symbols-outlined">network_check</span> Coordination </a>
    <a class="narrow-only menu-button" @click="open = !open"><span class="material-symbols-outlined">menu</span></a>
    <transition mode="out-in" name="slide-down">
      <div class="narrow-only menu" v-if="open" @click.passive="open = !open">
        <a :data-active="$props.active_page==='Monitor_Page'" @click="$emit('navigate', 'Monitor_Page')"><span class="material-symbols-outlined">monitoring</span> Monitor</a>
        <a :data-active="$props.active_page==='System_Page'" @click="$emit('navigate', 'System_Page')"><span class="material-symbols-outlined">dns</span> Wireless System</a>
        <a :data-active="$props.active_page==='Coordination_Page'" @click="$emit('navigate', 'Coordination_Page')"><span class="material-symbols-outlined">network_check</span> Coordination </a>
      </div>
    </transition>
  </div>
</template>
<style scoped>
.nav-bar{
  height: var(--bar-height);
  line-height: var(--bar-height);
  display: flex;
  gap: 1em;
  padding: 0 1em;
  border-bottom: 2px solid var(--primary-500);
  position: absolute;
  width: calc(100% - 2em);
  &>h1 {
    line-height: var(--bar-height);
    font-size: 1.5em;
    margin: 0;
  }
  &>a {
    color: var(--text-500);
    border-bottom: 0 solid transparent;
    transition: border 250ms ease;
    display: inline-flex;
    align-items: center;
    gap: 0.5em;
    cursor: pointer;
    &>.material-symbols-outlined {
      color: var(--text-200);
    }
    &:last-of-type {
      margin-left: auto;
    }
  }
  &>a:hover, &>a[data-active=true] {
    border-bottom: 4px solid var(--primary-500);
  }
  &>img {
    margin: 0.5em 0;
  }
  &>* {
    max-height: var(--bar-height);
  }
}

div.menu.narrow-only {
  max-height: calc(var(--bar-height) * 3);
  height: calc(var(--bar-height) * 3);
}

.menu {
  position: absolute;
  z-index: 999;
  left: 0;
  top: calc(var(--bar-height) + 4pt);
  width: calc(100% - 2em);
  flex-direction: column;
  background: var(--dark-200);
  padding: 0 1em;
  a {
    color: var(--text-500);
    display: flex;
    align-items: center;
    gap: 0.5em;
    span {
      opacity: 0.75;
    }
    &:not(:last-of-type) {
      border-bottom: 1pt solid var(--text-500);
    }
  }
}
.menu-button {
  width: 4em;
  justify-content: center
}

.slide-down-enter-active, .slide-down-leave-active {
  transition: height 150ms cubic-bezier(0.22, 1, 0.36, 1);
  overflow: hidden !important;
}
.slide-down-enter-from, .slide-down-leave-to {
  height: 0 !important;
}
</style>
<script>
export default {
  name: 'Navbar',
  props: [
    'active_page',
      'back'
  ],
  emits: ["navigate", "back"],
  data: ()=>{
    return {
      open: false
    }
  }
}
</script>