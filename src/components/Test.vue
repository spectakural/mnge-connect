<template>
  <div class="testpage">
    <button @click="sendTest">click</button>
    <button @click="startVideo">video</button>
    <video ref="videoPlayer" width="800" height="450" autoplay></video>
  </div>
</template>

<script>
import io from "socket.io-client";

export default {
  name: "Test",
  data() {
    return {
      socket: io("http://localhost:3001"),
      chunks: [],
      vidStatus: false,
      recorder: null,
      vidType: null,
    };
  },
  methods: {
    sendTest() {
      this.socket.emit("sendTest", { msg: "test" });
    },

    async startVideo() {
      const stream = await navigator.mediaDevices.getUserMedia({
        audio: false,
        video: true,
      });
      this.$refs.videoPlayer.srcObject = stream;
    },

    // async startVideo() {
    //   this.vidStatus = !this.vidStatus;
    //   if (this.vidStatus) {
    //     const stream = await navigator.mediaDevices.getDisplayMedia({
    //       audio: false,
    //       video: { mediaSource: "screen" },
    //     });

    //     this.recorder = new MediaRecorder(stream);

    //     const chunks = [];
    //     this.recorder.ondataavailable = (e) => {
    //       // chunks.push(e.data);
    //       // console.log(e.data);

    //       this.socket.emit("sendVideo", { data: e.data, type: e.data.type });
    //     };

    //     var testTO = setInterval(() => {
    //       this.recorder.stop();
    //       this.recorder.start();
    //     }, 100);
    //   } else {
    //     this.recorder.stop();
    //     clearInterval(testTO);
    //   }
    // },
  },
  mounted() {
    this.socket.on("videoData", (data) => {
      if (!this.vidType) {
        this.vidType = data.type;
      }
      console.log([data["data"]], { type: data["type"] });
      this.chunks[0] = data["data"];
      const blob = new Blob(this.chunks, { type: this.vidType });

      console.log("blob", blob);
      this.$refs.videoPlayer.src = URL.createObjectURL(blob);
      console.log(this.chunks);
    });
    this.socket.on("recieved", (data) => {});
  },
};
</script>

<style lang="less" scoped>
</style>