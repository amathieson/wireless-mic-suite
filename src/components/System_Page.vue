<script>
export default {
  name: "System_Page"
}
</script>

<template>
<div class="page-container">
    <details open>
      <summary class="category">Shure UR4D J5E</summary>
        <details>
          <summary class="receiver">01 John - 02 Jane</summary>
          <ul>
            <li class="transmitter">Shure UR1 - 01 John <code>600.000 MHz</code></li>
            <li class="transmitter">Shure UR1 - 02 Jane <code>600.000 MHz</code></li>
          </ul>
        </details>
        <details>
          <summary class="receiver">03 Alex - 04 Smith</summary>
          <ul>
            <li class="transmitter">Shure UR1 - 03 Alex <code>600.000 MHz</code></li>
            <li class="transmitter">Shure UR1 - 04 Smith <code>600.000 MHz</code></li>
          </ul>
        </details>
    </details>
    <details open>
      <summary class="category">Sennheiser EW-DX EM 2 Q</summary>
        <details>
          <summary class="receiver">31 John - 32 Jane</summary>
          <ul>
            <li class="transmitter">Sennheiser EW-DX SK - 31 John <code>600.000 MHz</code></li>
            <li class="transmitter">Sennheiser EW-DX SK - 32 Jane <code>600.000 MHz</code></li>
          </ul>
        </details>
        <details>
          <summary class="receiver">33 Alex - 34 Smith</summary>
          <ul>
            <li class="transmitter">Sennheiser EW-DX SK - 33 Alex <code>600.000 MHz</code></li>
            <li class="transmitter">Sennheiser EW-DX SK - 34 Smith <code>600.000 MHz</code></li>
          </ul>
        </details>
    </details>
<!--  </ul>-->
</div>
</template>

<style scoped>
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
}
summary:hover, .transmitter:hover {
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
  cursor: pointer;
  gap: 0.5em;
  background: var(--dark-500);
  transition: background 250ms ease;
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
  margin: 0.5em 1em;
}
</style>