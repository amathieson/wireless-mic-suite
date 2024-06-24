<template>
  <div class="nav-bar">
    <img src="/GAOS-logo-light-small.png" alt="GAOS">
    <a href="#"><span class="material-symbols-outlined">monitoring</span> Monitor</a>
    <a href="#"><span class="material-symbols-outlined">dns</span> Wireless System</a>
    <a href="#"><span class="material-symbols-outlined">network_check</span> Coordination </a>
  </div>
</template>
<style scoped>
.nav-bar{
  --bar-height: 36pt;
  height: var(--bar-height);
  line-height: var(--bar-height);
  display: flex;
  gap: 1em;
  padding: 0 1em;
  border-bottom: 2px solid var(--primary-500);
  &>a {
    color: var(--text-500);
    border-bottom: 0 solid transparent;
    transition: border 250ms ease;
    display: inline-flex;
    align-items: center;
    gap: 0.5em;
    &>.material-symbols-outlined {
      color: var(--text-200);
    }
  }
  &>a:hover, &>a[active] {
    border-bottom: 4px solid var(--primary-500);
  }

  &>img {
    margin: 0.5em 0;
  }
  &>* {
    max-height: var(--bar-height);
  }
}
</style>
<script>
export default {
  name: 'Navbar'
}
</script>