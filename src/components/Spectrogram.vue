<template>
  <div>
    <h1>時頻譜</h1>
    <canvas :id="_uid" :width="canvasWidth" :height="canvasHeight"></canvas>
  </div>
</template>

<script>
export default {
  name: "Spectrogram",
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
  },
  data: function () {
    return {
      dataArray: [],
      dataArrayHistory: [],
    };
  },
  methods: {
    draw: function () {
      requestAnimationFrame(this.draw);

      this.analyser.getByteFrequencyData(this.dataArray);

      this.dataArrayHistory.push(this.dataArray.slice());
      if (this.dataArrayHistory.length > 256) {
        this.dataArrayHistory.shift();
      }

      this.canvasCtx.fillStyle = this.fillStyle;
      this.canvasCtx.fillRect(0, 0, this.canvasWidth, this.canvasHeight);

      let pixelWidth = this.canvasWidth / this.bufferLength;
      let pixelHeight = this.canvasHeight / 256;

      for (var y = 0; y < this.dataArrayHistory.length; y++) {
        for (var x = 0; x < this.dataArrayHistory[y].length; x++) {
          let d = this.dataArrayHistory[y][x];
          this.canvasCtx.fillStyle = `rgb(${d},${0},${d})`;
          this.canvasCtx.fillRect(
            x * pixelWidth,
            y * pixelHeight,
            pixelWidth,
            pixelHeight
          );
        }
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

        // unique canvas identifier using component ID
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
