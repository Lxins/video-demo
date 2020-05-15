<template>
  <div id="wrapper">
    <div>
      <div style="margin-top:10vh">
        输入摄像头名称:<input v-model="cameraName" type="text">
      </div>
      <button @click="pushFlow">推流</button>
    </div>
    <div class="open-link" style="margin-top: 50px">
      <p>直播地址: <a @click="openLinks" href="javascript:;">http://106.75.119.161:81/</a></p>
      <p>操作指南: <a @click="openManual" href="javascript:;">http://106.75.119.161:81/manual.html</a></p>
    </div>
  </div>
</template>

<script>
const shell = require('electron').shell

export default {
  name: "landing-page",
  data() {
    return {
      cameraName: ''
    }
  },
  created() {
    
  },
  methods: {
    openLinks () {
      shell.openExternal('http://106.75.119.161:81/')
    },
    openManual () {
      shell.openExternal('http://106.75.119.161:81/manual.html')
    },
    pushFlow () {
      const path = require("path");
      const ffmpeg = require("fluent-ffmpeg");
      // const inputPath = path.join(__dirname, "./video.mp4");
      const inputPath = `video=HP TrueVision HD Camera`;
      // const inputPath = `video=${this.cameraName}`;
      // const ffmpegPath = process.resourcesPath + "/ffmpeg.exe";
      const ffmpegPath = path.join(__dirname, "/ffmpeg.exe");
      // const outputPath = "rtmp://106.75.119.161:1935/hls/test";
      const outputPath = "rtmp://106.75.119.161:1935/hls/appLive1002";

      console.log(process.resourcesPath)

      let command = new ffmpeg(inputPath)
        .setFfmpegPath(ffmpegPath)
        .inputOptions("-f dshow")
        .size("800x600")
        .on("start", function(commandLine) {
          console.log(1111)
          console.log("start push......." + commandLine);
          console.log("start command......." + command);
        })
        .on("end", function() {
          console.log("storp push........");
          // stopPush();
        })
        .on("error", function(err, stdout, stderr) {
          console.log(2222)
          console.log("error:" + err.message);
          // console.log("stdout:" + stdout);
          // console.log("stderr:" + stderr);
          // stopPush();
        })
        .addOptions([
          // '-preset veryfast',
          // "-rtsp_transport tcp",
          // "-f rtsp",
          '-vcodec libx264',
          '-preset veryfast',
          '-crf 22',
          '-maxrate 1000k',
          '-bufsize 3000k',
          '-acodec libmp3lame',
          '-ac 2',
          '-ar 44100',
          '-b:a 96k'
        ])
        .format('flv')
        .pipe(outputPath, { end: true });
    }
  }
};
</script>

<style lang="scss" scoped>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
#wrapper {
  text-align: center;
  .open-link {
    p {
      &:nth-of-type(2) {
        margin-top: 15px;
        margin-left: 82px;
      }
    }
  }
}

button {
  margin-top: 30%;
  font-size: 32px;
}
</style>
