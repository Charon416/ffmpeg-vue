<script setup lang="ts">
let selectedFile: File;

import { FFmpeg } from '@ffmpeg/ffmpeg';
import { toBlobURL } from '@ffmpeg/util';

const btnSelect = () => {
  let d = document.createElement("input")
  d.type = "file"
  d.onchange = () => {
    if (d.files && d.files.length) {
      selectedFile = d.files[0]
      console.log(selectedFile)
    }
  }
  d.click()
}
const btnTranscodeClicked = async () => {
  let ffmpeg = new FFmpeg();
  await ffmpeg.load({
    coreURL: await toBlobURL('ffmpegcore/ffmpeg-core.js', "text/javascript"),
    wasmURL: await toBlobURL('ffmpegcore/ffmpeg-core.wasm', "application/wasm"),
  })
  console.log(ffmpeg)

  ffmpeg.on("progress", progress => {
    console.log(progress)
  })

  let fileData = await selectedFile.arrayBuffer();
  let data = new Uint8Array(fileData);
  await ffmpeg.writeFile("input", data);
  await ffmpeg.exec(["-i", "input", "out.mp4"])
  
  let outFileData = await ffmpeg.readFile("out.mp4");
  ffmpeg.terminate();

  let e = document.createElement("a");
  e.download = "out.mp4";
  e.href = URL.createObjectURL(new Blob([outFileData]));
  e.click();
}
</script>

<template>
  <div>
    <div>
      <h2>1、视频转码</h2>
      
    </div>
    <button @click="btnSelect">上传</button>
    <button @click="btnTranscodeClicked">转化</button>
  </div>
</template>

<style scoped></style>
