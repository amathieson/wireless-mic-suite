<script>
export default {
  name: "Device_Config",
  props: ["active"],
}
</script>

<template>
  <div class="container" :active="active">
    <div class="header">
      <div class="device-image">
        <span class="material-symbols-outlined">dns</span>
      </div>
      <div class="device-info">
        <h1>Shure UR4D+ 01 John - 02 Jane</h1>
        <code><span class="material-symbols-outlined">tag</span> 2085818421</code>
        <code><span class="material-symbols-outlined">terminal</span> 1.171</code>
      </div>
    </div>
    <div class="buttons">
      <button class="primary"><span class="material-symbols-outlined">save</span> Save Changes</button>
      <button><span class="material-symbols-outlined">point_scan</span> Identify</button>
    </div>
    <div class="config">
      <section>
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
    </div>
  </div>
</template>

<style scoped>
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
}
</style>