<template>
  <h5>Manchester</h5>
  <div class="waveform">
    <svg :viewBox="viewBox" class="waveform-chart">
      <text x="0" y="55" font-family="Arial" font-size="16">+5V</text>
      <text x="7" y="105" font-family="Arial" font-size="16">0V</text>

      <!-- Horizontal line -->
      <line
        :x1="padding"
        y1="100"
        :x2="svgWidth + padding"
        y2="100"
        stroke="black"
        stroke-width="0.5"
      />

      <!-- Vertical grid lines -->
      <line
        v-for="(lineX, index) in gridLines"
        :key="index"
        :x1="lineX"
        y1="0"
        :x2="lineX"
        y2="200"
        stroke="gray"
        stroke-width="0.5"
      />

      <!-- Waveform chart -->
      <polyline
        :points="waveformPoints"
        fill="none"
        stroke="black"
        stroke-width="2"
      />

      <!-- Bit labels -->
      <text
        v-for="(label, index) in labels"
        :key="index"
        :x="label.x + padding"
        y="40"
        font-family="Arial"
        font-size="16"
      >
        {{ label.value }}
      </text>
    </svg>
  </div>
</template>

<script>
export default {
  props: ["binaryData"],
  data() {
    return {
      waveformPoints: "",
      labels: [],
      viewBox: "",
      svgWidth: 0,
      padding: 0,
      gridLines: [] 
    };
  },
  watch: {
    binaryData() {
      this.generateWaveform();
    },
  },
  mounted() {
    this.generateWaveform();
  },
  methods: {
    generateWaveform() {
      const points = [];
      const labels = [];
      const gridLines = []; 
      let x = this.padding;
      const step = 50;
      const high = 50;
      const low = 100;
      const sequenceLength = this.binaryData.length;

      // if (this.binaryData[0] === "0") {
      //   points.push(`${x},${high}`);
      //   x += step;
      //   points.push(`${x},${high}`, `${x},${low}`);
      //   x += step;
      //   points.push(`${x},${low}`);
      // } else {
      //   points.push(`${x},${low}`);
      //   x += step;
      //   points.push(`${x},${low}`, `${x},${high}`);
      //   x += step;
      //   points.push(`${x},${high}`);
      // }

      for (let i = 0; i < sequenceLength; i++) {
        const bit = this.binaryData[i];
        labels.push({ x: x + step, value: bit });

        if (bit === "0") {
          points.push(`${x},${high}`);
          x += step;
          points.push(`${x},${high}`, `${x},${low}`);
          x += step;
          points.push(`${x},${low}`);
        } else {
          points.push(`${x},${low}`);
          x += step;
          points.push(`${x},${low}`, `${x},${high}`);
          x += step;
          points.push(`${x},${high}`);
        }
      }

      for (let gridX = 0; gridX <= x; gridX += 100) {
        gridLines.push(gridX);
      }

      this.waveformPoints = points.join(" ");
      this.labels = labels;
      this.gridLines = gridLines; 
      this.svgWidth = sequenceLength * (step * 2);
      this.viewBox = `0 0 ${this.svgWidth + this.padding} 200`;
    },
  },
};
</script>

<style scoped>
.waveform {
  display: block;
}

.waveform-chart {
  max-width: 100%;
  height: 200px;
}
</style>
