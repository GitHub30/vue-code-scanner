<template>
    <div class="c-video-wrapper">
        <video class="scanner" ref="scanner" :width="width" :height="height"></video>
        <div class="laser"></div>
    </div>
</template>

<script>
  import {BrowserQRCodeReader} from '@zxing/library';

  export default {
    name: "CodeScanner",
    props: {
      width: {type: String, default: "480"},
      height: {type: String, default: "320"},
    },
    data() {
      return {
        QRCodeReader: null
      }
    },
    mounted() {
      this.initScanner();
    },
    methods: {
      // init
      initScanner() {
        this.activateQRcode();
      },

      // decode from input device
      activateQRcode() {
        this.QRCodeReader = new BrowserQRCodeReader();
        this.QRCodeReader.decodeFromInputVideoDevice(undefined, this.$refs.scanner).then((result) => {
          this.getScannerResult(result);
        }).catch((err) => {
          console.log(err);
        });
      },

      // get result
      getScannerResult(result) {
        this.$emit('result', result);
      },

      resetScanner() {
        this.QRCodeReader.reset();
      }
    },
    // Close connection to scanner, when component is destroyed
    beforeDestroy() {
      this.resetScanner();
    }
  }
</script>

<style scoped>
    .scanner {
        max-width: 100%;
        object-fit: cover;
    }

    .c-video-wrapper {
        position: relative;
        height: auto;
        overflow: hidden;
    }

    .c-video-wrapper .laser {
        width: 90%;
        margin-left: 5%;
        background-color: red;
        height: 1px;
        position: absolute;
        top: 10%;
        z-index: 3;
        animation: scanning 3s infinite;
    }

    @keyframes scanning {
        50% {
            transform: translateY(240px);
        }
    }
</style>