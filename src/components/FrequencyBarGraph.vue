<template>
  <div>
    <h1>頻率圖</h1>
    <canvas :id="_uid" :width="canvasWidth" :height="canvasHeight"></canvas>
  </div>
</template>

<script>
export default {
  name: "FrequencyBarGraph",
  props: {
    canvasWidth: {
      type: Number,
      default: 750,
    },
    canvasHeight: {
      type: Number,
      default: 500,
    },
    fillStyle: {
      type: String,
      default: "rgba(0,0,0)",
    },
    strokeStyle: {
      type: String,
      default: "rgb(255, 255, 255)",
    },
  },
  data: function () {
    return {};
  },
  methods: {
    map: function (n, nMin, nMax, rMin, rMax) {
      return ((n - nMin) * (rMax - nMin)) / (nMax - nMin) + rMin;
    },
    draw: function () {
      requestAnimationFrame(this.draw);

      this.analyser.getByteFrequencyData(this.dataArray);

      this.canvasCtx.fillStyle = this.fillStyle;
      this.canvasCtx.fillRect(0, 0, this.canvasWidth, this.canvasHeight);

      var barWidth = this.canvasWidth / this.bufferLength;
      var x = 0;

      for (var i = 0; i < this.bufferLength; i++) {
        let d = this.dataArray[i];

        let barHeight = this.map(d, 0, 255, 0, this.canvasHeight);

        this.canvasCtx.fillStyle = `rgb(${d},${0},${d})`;
        this.canvasCtx.fillRect(
          x,
          this.canvasHeight - barHeight,
          barWidth,
          barHeight
        );

        x += barWidth + 1;
      }
    },
  },
  mounted: function () {
    

    navigator.mediaDevices
      .getUserMedia({ audio: true })
      .then((stream) => {
        this.audioCtx = new (window.AudioContext ||
          window.webkitAudioContext)();
        this.analyser = this.audioCtx.createAnalyser();

        this.source = this.audioCtx.createMediaStreamSource(stream);
        this.source.connect(this.analyser);

        this.analyser.fftSize = 256;

        this.bufferLength = this.analyser.frequencyBinCount;
        this.dataArray = new Uint8Array(this.bufferLength);

        this.canvas = document.getElementById(this._uid);
        this.canvasCtx = this.canvas.getContext("2d");

        this.canvasCtx.clearRect(0, 0, this.canvasWidth, this.canvasHeight);

        this.draw();
      })
      .catch(function (err) {
        console.error(err);
      });
  },
};
</script>
