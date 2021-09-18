<template>
  <div>
  <notification-wrapper />
    <div class="flex justify-between px-2 py-4 w-full bg-white md:fixed">
      <button @click="fetchData" class="shadow bg-purple-500 hover:bg-purple-400 focus:shadow-outline focus:outline-none text-white font-bold py-2 px-4 rounded" type="button">
        Generate
      </button>
      <select class="form-select w-24 pt-0 pb-0 shadow border border-gray-600 border-solid cursor-pointer" v-model="colorFormat" @change="fetchData">
        <option class="cursor-pointer" value="hex">Hex</option>
        <option class="cursor-pointer" value="rgba">rgba</option>
      </select>
    </div>
  <div class="grid md:grid-cols-5 grid-cols-1 md:min-w-full min-h-screen">
    <div 
      v-for="(row, key) in data" :key="key" 
      @click="copyColor(row)" 
      :id="`shade${key}`"
      :style="`background-color: ${row.hex};`" 
    >
      <h3 v-if="colorFormat == 'hex'" class="text-black cursor-pointer text-lg flex flex-col items-center h-full md:min-h-screen justify-center" :style="`color: ${row.text};`">{{row.hex}}</h3>
      <h3 v-else class="cursor-pointer text-lg flex flex-col items-center h-full md:min-h-screen justify-center" :style="`color: ${row.text};`">{{row.rgba}}</h3>
    </div>
  </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      colorFormat: 'hex',
      data: [
        {rgba: '', hex: '', text: ''},
        {rgba: '', hex: '', text: ''},
        {rgba: '', hex: '', text: ''},
        {rgba: '', hex: '', text: ''},
        {rgba: '', hex: '', text: ''}
      ]
    }
  },
  mounted() {
    this.fetchData()
  },
  methods: {
    hexToRGBA(hex) {
      // remove invalid characters
      hex = hex.replace(/[^0-9a-fA-F]/g, '');

      if (hex.length < 5) { 
          // 3, 4 characters double-up
          hex = hex.split('').map(s => s + s).join('');
      }

      // parse pairs of two
      let rgba = hex.match(/.{1,2}/g).map(s => parseInt(s, 16));

      // alpha code between 0 & 1 / default 1
      rgba[3] = rgba.length > 3 ? parseFloat(rgba[3] / 255).toFixed(2): 1;

      return 'rgba(' + rgba.join(', ') + ')';
  },
    invertColor(hex) {
      if (hex.indexOf("#") === 0) {
        hex = hex.slice(1);
      }
      // convert 3-digit hex to 6-digits.
      if (hex.length === 3) {
        hex = hex[0] + hex[0] + hex[1] + hex[1] + hex[2] + hex[2];
      }
      // if (hex.length !== 6) {
      //   throw new Error("Invalid HEX color.");
      // }
      var r = parseInt(hex.slice(0, 2), 16),
        g = parseInt(hex.slice(2, 4), 16),
        b = parseInt(hex.slice(4, 6), 16);
      // if (bw) {
      //   // http://stackoverflow.com/a/3943023/112731
        return r * 0.299 + g * 0.587 + b * 0.114 > 186 ? "#000000" : "#FFFFFF";
      // }
      // invert color components
      r = (255 - r).toString(16);
      g = (255 - g).toString(16);
      b = (255 - b).toString(16);
      // pad each with zeros and return
      return "#" + padZero(r) + padZero(g) + padZero(b);
    },
    async fetchData() {
      var randomColor = require('randomcolor');
      var response = randomColor({
        count: 5,
        luminosity: 'random',
        hue: 'random'
      });
      response.map( (x, key) => {

        this.data[key].hex = x,
        this.data[key].rgba = this.hexToRGBA(x),
        this.data[key].text = this.invertColor(x)
      })
    },
    copyColor(row) {
      this.colorFormat === 'hex' ? this.$clipboard(row.hex): this.$clipboard(row.rgba)
      this.$notification({
        text: 'copied?',
        color: 'green',
      });
    }
  }
}
</script>