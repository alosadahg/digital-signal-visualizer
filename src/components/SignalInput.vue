<template>
  <h4 class="text-center">Digital Signal Visualizer</h4>
  <form @submit.prevent="encodeSignal" class="row g-2 align-items-center justify-content-center">
    <div class="col-auto">
      <label class="col-form-label" for="binaryInput">Binary Input</label>
    </div>
    <div class="col-4 col-md-4">
      <input
        id="binaryInput"
        class="form-control"
        type="text"
        v-model="binaryData"
        placeholder="Enter binary data (e.g., 1010)"
      />
    </div>
    <div class="col-auto">
      <button
        class="btn btn-primary"
        type="submit"
        :disabled="isInputNotBinary"
      >
        Generate Digital Signal
      </button>
    </div>
  </form>

  <div v-if="encodedData" class="text-center mt-3">
      <div class="my-2">
        <NRZL :binaryData="encodedData"/>
      </div>
      <div class="my-2">
        <NRZI :binaryData="encodedData"/>
      </div>
      <div class="my-2">
        <BipolarAMI :binaryData="encodedData"/>
      </div>
      <div class="my-2">
        <PseudoTernary :binaryData="encodedData"/>
      </div>
      <div class="my-2">
        <ManchesterEncoding :binaryData="encodedData"/>
      </div>
      <div class="my-2">
        <DifferentialManchester :binaryData="encodedData"/>
      </div>
    </div>
</template>

<script>
import NRZL from './NRZL.vue';
import NRZI from './NRZI.vue';
import BipolarAMI from './BipolarAMI.vue';
import PseudoTernary from './PseudoTernary.vue';
import ManchesterEncoding from './ManchesterEncoding.vue';
import DifferentialManchester from './DifferentialManchester.vue';

export default {    
components: {
  NRZL,
  NRZI,
  BipolarAMI,
  PseudoTernary,
  ManchesterEncoding,
  DifferentialManchester
},
computed: {
  isInputNotBinary() {
    return !/^[01]+$/.test(this.binaryData);
  }
},
data() {
  return {
    binaryData: "",
    encodedData: ""
  };
},
methods: {
  encodeSignal() {
    if (!this.isInputNotBinary) {
      this.encodedData = this.binaryData;
    }
  },
},
};
</script>

<style scoped>
.invalid-feedback {
display: block;
}
</style>
