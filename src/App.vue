<template>
  <div id="app" style="margin-left:10%">
    <Waveform :canvasHeight='150'/>
    <FrequencyBarGraph :canvasHeight='250'/>
    <Spectrogram :canvasHeight='250'/>
  </div>
</template>

<script>
import Waveform from './components/Waveform.vue'
import FrequencyBarGraph from './components/FrequencyBarGraph.vue'
import Spectrogram from './components/Spectrogram.vue'

export default {
  name: 'App',
  components: {
    Waveform,
    FrequencyBarGraph,
    Spectrogram
  }
}
</script>
<style>
body {
  background-color: black ;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
}
</style>
