<template>
  <div id="app">
    <ul>
      <li>{{ stocks.label }}</li>
      <ul v-for="row in stocks.row" :key="row.level">
        <li>Level: {{ row.level }}</li>
        <ul v-for="location in row.locations" :key="location.name">
          <li>
            <span
              @click="handleToggle(getLocation(location.name))"
              :class="
                getLocation(location.name).collapsed
                  ? 'toggled-off'
                  : 'toggled-on'
              "
            >
              Location: {{ location.name }}
            </span>
          </li>
          <ul
            v-for="stock in location.stock"
            :key="stock.product"
            :class="
              getLocation(location.name).collapsed ? 'collapsed' : 'opened'
            "
          >
            <li>Product: {{ stock.product }}</li>
            <li>Quantity: {{ stock.qty }}</li>
            <li>Replenishment: {{ stock.replenishment }}</li>
          </ul>
        </ul>
      </ul>
    </ul>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import Component from 'vue-class-component'

type LocationView = {
  location: string
  collapsed: boolean
}

@Component
export default class App extends Vue {
  stocks = {}
  locations = [] as LocationView[]

  created() {
    fetch('stockData.json')
      .then((response) => response.json())
      .then((data) => {
        this.stocks = data

        data.row.forEach((row: any) => {
          row.locations.forEach((location: any) => {
            this.locations.push({
              location: location.name,
              collapsed: false,
            })
          })
        })
      })
  }

  getLocation(location: string) {
    return this.locations.find((locationData: LocationView) => {
      return locationData.location === location
    })
  }

  handleToggle(locationData: LocationView) {
    const i = this.locations.findIndex((data: LocationView) => {
      return data.location === locationData.location
    })

    this.locations[i].collapsed = !this.locations[i].collapsed
  }
}
</script>

<style>
body,
html {
  margin: 0px;
  padding: 0px;
}

#app {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #ffffff;
  background-color: #282c34;
  min-height: 100vh;
  display: flex;
  font-size: calc(10px + 2vmin);
}

ul {
  list-style-type: none;
}

.toggled-off {
  -webkit-user-select: none; /* Safari 3.1+ */
  -moz-user-select: none; /* Firefox 2+ */
  -ms-user-select: none; /* IE 10+ */
  user-select: none;
}

.toggled-off::before {
  cursor: pointer;
  content: '\2610';
  color: #646464;
}

.toggled-on::before {
  cursor: pointer;
  content: '\2611';
  color: #9c9c9c;
}

.collapsed {
  display: none;
}

.opened {
  display: block;
}
</style>
