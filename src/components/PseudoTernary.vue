<template>
  <h5>Pseudoternary</h5>
  <div class="waveform">
    <svg :viewBox="viewBox" class="waveform-chart">
      <text x="0" y="55" font-family="Arial" font-size="16">+5V</text>
      <text x="7" y="105" font-family="Arial" font-size="16">0V</text>
      <text x="0" y="155" font-family="Arial" font-size="16">-5V</text>
      <!-- Chart -->
      <line
        :x1="padding"
        y1="100"
        :x2="svgWidth + padding"
        y2="100"
        stroke="black"
        stroke-width="0.5"
      />
      <polyline
        :points="waveformPoints"
        fill="none"
        stroke="black"
        stroke-width="2"
      />
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
      currentLevel: 0,
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
      let x = this.padding;
      const step = 100;
      const high = 50;
      const low = 100;
      const negative = 155;
      const sequenceLength = this.binaryData.length;

      if (this.binaryData[0] === "0") {
        points.push(`${x},${high}`);
        this.currentLevel = high;
      } else {
        points.push(`${x},${low}`);
        this.currentLevel = low;
      }

      let prevState = this.currentLevel;
      let lastZeroState = this.currentLevel;

      for (let i = 0; i < sequenceLength; i++) {
        const bit = this.binaryData[i];
        labels.push({ x: x + step / 2, value: bit });

        if (i > 0) {
          const prevBit = this.binaryData[i - 1];
          //console.log("lastOneState: " + lastOneState + " prevState: " + prevState);
          if (bit === "0") {
            if (lastZeroState == high) {
              points.push(`${x},${prevState}`, `${x},${negative}`);
              prevState = negative;
              lastZeroState = negative;
            } else {
              points.push(`${x},${prevState}`, `${x},${high}`);
              prevState = high;
              lastZeroState = high;
            }
          } else {
            if(prevBit === '0') {
                if(prevState == high) {
                    points.push(`${x},${high}`, `${x},${low}`);
                } else {
                    points.push(`${x},${negative}`, `${x},${low}`);
                }
                //console.log("prevBitState: " + prevState);
            }
            prevState = low;
          }
        }
        //console.log("bit:" + bit + " state:" + prevState);
        points.push(`${x + step},${prevState}`);
        x += step;
      }

      this.waveformPoints = points.join(" ");
      this.labels = labels;
      this.svgWidth = sequenceLength * step;
      this.viewBox = `0 0 ${this.svgWidth + this.padding} 200`; // Include padding in the viewBox width
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
