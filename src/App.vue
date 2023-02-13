<template>
  <input
    ref="video1"
    type="file"
    @change="handleChange"
  >
  <video
    :src="video"
    controls
  />
  <br>
  <!-- <button @click="transcode">
    Start
  </button> -->
  <p>{{ message }}</p>
</template>

<script>
import { createFFmpeg, fetchFile } from "@ffmpeg/ffmpeg";
import { defineComponent, ref } from "vue";


export default defineComponent({
  name: "App",
  setup() {
    // app state
    const ffmpeg = createFFmpeg({
      log: true,
    });
    const message = ref("Click Start to Transcode");
    let video = ref(null);
  
    async function handleChange(e) {
 
      const file = e.target.files[0]

     await ffmpeg.load()
    ffmpeg.FS("writeFile", "input.mov", await fetchFile(file));
    await ffmpeg.run("-i", "input.mov", "output.mp4");
    const data = ffmpeg.FS("readFile", "output.mp4");
    video.value = URL.createObjectURL(
      new Blob([data.buffer], { type: "video/mp4" })
    );
    }
  
    return {
      video,
      message,

      handleChange
    };
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
