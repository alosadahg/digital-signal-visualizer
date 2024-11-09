<template>
  <h5>Differential Manchester</h5>
  <!-- Radio buttons for initial level selection -->
  <div class="radio-container">
    <label>
      <input
        class="form-check-input"
        type="radio"
        value="high"
        v-model="initialLevel"
      />
      Initially High
    </label>
    <label>
      <input
        class="form-check-input"
        type="radio"
        value="low"
        v-model="initialLevel"
      />
      Initially Low
    </label>
  </div>

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
      initialLevel: "high",
      svgWidth: 0,
      high: 50,
      low: 100,
      padding: 0,
      gridLines: [],
      state: 0,
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
      const high = this.high;
      const low = this.low;
      const sequenceLength = this.binaryData.length;
      this.state = high;

      for (let i = 0; i < sequenceLength; i++) {
        const bit = this.binaryData[i];
        labels.push({ x: x + step, value: bit });

        if(i === 0 && this.initialLevel === "low") {
            this.state = low;
        }

        if (bit === "0") {
          console.log("before-state: " + this.state);
          points.push(`${x},${this.opposite()}`);
          console.log("after-state: " + this.state);
          x += step;
          points.push(`${x},${this.state}`, `${x},${this.opposite()}`);
          x += step;
          points.push(`${x},${this.state}`);
        } else {
          points.push(`${x},${this.state}`);
          x += step;
          points.push(`${x},${this.state}`, `${x},${this.opposite()}`);
          x += step;
          points.push(`${x},${this.state}`);
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
    opposite() {
        this.state = (this.state === this.high) ? this.low : this.high;
        return this.state;
    }
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

.radio-container {
  display: inline-flex;
  gap: 40px; /* Adjust this value for more or less spacing */
}
</style>
