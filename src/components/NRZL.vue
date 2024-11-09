<template>
  <h5>NRZ-L</h5>
  <div class="waveform">
    <svg :viewBox="viewBox" class="waveform-chart">
      <text x="0" y="55" font-family="Arial" font-size="16">+5V</text>
      <text x="7" y="105" font-family="Arial" font-size="16">0V</text>
      <!-- Chart -->
      <line :x1="padding" y1="100" :x2="svgWidth + padding" y2="100" stroke="black" stroke-width="0.5"/>
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
      <polyline :points="waveformPoints" fill="none" stroke="black" stroke-width="2"/>
      <text v-for="(label, index) in labels" :key="index" :x="label.x + padding" y="40" font-family="Arial" font-size="16">{{ label.value }}</text>
    </svg>
  </div>
</template>

<script>
export default {
  props: ['binaryData'],
  data() {
    return {
      waveformPoints: '',
      labels: [],
      gridLines: [],
      viewBox: '',
      svgWidth: 0,
      padding: 0
    };
  },
  watch: {
    binaryData() {
      this.generateWaveform(); 
    }
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
      const step = 100;
      const high = 50;
      const low = 100;
      const sequenceLength = this.binaryData.length;

      if (this.binaryData[0] === '1') {
        points.push(`${x},${high}`);
      } else {
        points.push(`${x},${low}`);
      }

      for (let i = 0; i < sequenceLength; i++) {
        const bit = this.binaryData[i];
        labels.push({ x: x + step / 2, value: bit });
        
        if (i > 0) {
          const prevBit = this.binaryData[i - 1];
          if (bit === '1' && prevBit !== '1') {
            points.push(`${x},${low}`, `${x},${high}`);
          } else if (bit === '0' && prevBit !== '0') {
            points.push(`${x},${high}`, `${x},${low}`);
          }
        }
        
        if (bit === '1') {
          points.push(`${x + step},${high}`);
        } else {
          points.push(`${x + step},${low}`);
        }
        x += step;
      }

      for (let gridX = 0; gridX <= x; gridX += 100) {
        gridLines.push(gridX);
      }


      this.waveformPoints = points.join(' ');
      this.labels = labels;
      this.gridLines = gridLines; 
      this.svgWidth = sequenceLength * step;
      this.viewBox = `0 0 ${this.svgWidth + this.padding} 200`;  
    }
  }
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
