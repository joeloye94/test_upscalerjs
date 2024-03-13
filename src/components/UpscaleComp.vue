<template>
    <div>
      <input type="file" ref="fileInput" @change="onFileChange"/>

      <div style="display:flex; gap:2">
        <div v-if="data.imageBefore" :key="data.imageBefore" style="width:50%;">

          <img :src="data.imageBefore" style="width:100%"/>

        </div>
        <div :key="data.imageAfter" style="width:50%;">

          <div v-if="data.upscaling" >Upscaling</div>
          <img :src="data.imageAfter" v-if="data.imageAfter" style="width:100%"/>

        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import {reactive} from "vue";
  // import * as tf from "@tensorflow/tfjs";
  import Upscaler from 'upscaler';

  //https://codesandbox.io/p/devbox/upscalerjs-examples-upload-uxmcl?file=%2Findex.js%3A1%2C13-1%2C15
  //https://thekevinscott.com/super-resolution-with-js/
  const data = reactive({
    imageBefore:null,
    imageAfter:null,
    upscaling:false
  })




  // const upscaler = new Upscaler();
  // upscaler.upscale('/path/to/image').then(upscaledImage => {
  //   console.log(upscaledImage); // base64 representation of image src
  // });
  const upscaler = new Upscaler({
    // warmupSizes: [[64, 64]],
  });

  const onFileChange = async (ev) => {
    // console.log(ev.target.files)
    if(!ev.target.files.length) return;

    const file = ev.target.files[0]
    const base64Img = await convertToBase64(file)
    data.imageBefore = base64Img;

    const upscaledImg = await upscale(file)
    data.imageAfter = upscaledImg;
  }

  const upscale = async (file) => {
    const base64Img = await convertToBase64(file)

    const upscaledImgSrc = await upscaler.upscale(base64Img, {
      patchSize: 64,
      padding: 5,
    });
    return upscaledImgSrc
    // console.log(upscaledImgSrc)
  }

  function convertToBase64(file){
    return new Promise((res,rej)=>{
        if(!file) return;
        
        const fileReader = new FileReader();
        fileReader.onload = () => {
            const srcData = fileReader.result;
            // console.log('base64:', srcData)
            res(srcData)
        };
        fileReader.onerror = () => {
            rej(null)
        };
        fileReader.readAsDataURL(file);
        
    })    
}
  </script>
  
  <style>
  
  </style>