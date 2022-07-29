<template>
  <div class="camera-wrapper">
    
    <p class="cabecalho">
      
      Comparação de imagens
      
    </p>
    <div ref="dimensions" class="dimensions">

      <vue-web-cam
        v-show="!url_selfie"
        ref="webcam"
        :width="width"
        :height="height"
        :resolution="getDimensions"
        :device-id="deviceId"
        @cameras="onCameras"
      />
    </div>
    <div v-if="!url_selfie" class="camera-panel">
      <br>
      <button class="button" v-if="cameraStart" @click="onStop">Parar Camera</button>
      <button class="button" v-else @click="onStart">Iniciar Camera</button>
      <button class="button" v-if="cameraStart" @click="onCapture">Captura Imagem</button>
    </div>

    <div v-if="!url_foto && url_selfie" class="camera-panel">
      <br>
      <input class="button_img" type="file" @change="onFileChange" />
    </div>

    <div class="camera-panel">
      <br>
      <img class="img" v-if="url_selfie" :src="url_selfie" alt="camera-img"/>&nbsp;
      <font color="#008000" v-if="resultado && resultado.code == 0 && resultado.message == 'Deu Match'"><strong> {{resultado.message}} </strong></font>
      <font color="#B22222" v-if="resultado && resultado.code == 0 && resultado.message == 'Não Deu Match'"><strong> {{resultado.message}} </strong></font>
      &nbsp;<img class="img" v-if="url_foto" :src="url_foto" alt="documento-img"/>
    </div>
    
    <p class="camera-panel" v-if="resultado && resultado.code == 1"> {{resultado.message}} </p>
   
    <div class="camera-panel">
      <br>
      <button v-if="this.foto && this.url_selfie" class="button" @click="onComparar">Comparar</button>
      <button v-if="this.foto || this.url_selfie" class="button" @click="onClear">Voltar</button>
    </div>
    <br>  
  </div>
</template>

<script>

import axios from "axios";

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },

  data() {
    return {
      cameraStart:true,
      url_selfie: null,
      camera: null,
      deviceId: null,
      devices: [],
      width: 842,
      height: 521,
      url_foto: null,
      foto: null,
      resultado: null,
    };
  },

  mounted() {
    /*
    const { width, height } = this.$refs.dimensions.getBoundingClientRect();
    this.width = Math.round(width);
    this.height = Math.round(height);
    */
  },
  computed: {
    getDimensions() {
      /*
      function detectMob() {
        const toMatch = [
          /Android/i,
          /webOS/i,
          /iPhone/i,
          /iPad/i,
          /iPod/i,
          /BlackBerry/i,
          /Windows Phone/i,
        ];

        return toMatch.some((toMatchItem) =>
          toMatchItem.test(navigator.userAgent)
        );
      }
      const mobDevice = detectMob();
      console.log(this.width, this.height);
      return mobDevice
        ? { width: this.height, height: this.width }
        : { width: this.width, height: this.height };

      */
      return { width: this.height, height: this.width };
    },
  },

  watch: {
    camera: function (id) {
      this.deviceId = id;
    },
    devices: function () {
      // Once we have a list select the first one
      console.log(this.devices);
      //const [first, ...tail] = this.devices;
      const [first] = this.devices;
      if (first) {
        this.deviceId = first.deviceId;
      }
    },
  },
  
  methods: {
    onCameras(cameras) {
      this.devices = cameras;
    },
    onCapture() {
      this.url_selfie = this.$refs.webcam.capture();
    },
    
    onStart() {
      this.$refs.webcam.start();
      this.cameraStart = true
    },
    onStop(){
      this.$refs.webcam.stop();
      this.cameraStart = false;
    },

    onClear() {
      this.url_selfie = null;
      this.url_foto = null;
      this.foto = null;
      this.resultado = null;
      this.$refs.webcam.start();
    },
    
    async onComparar(){


      let selfie = this.dataURLtoFile(this.url_selfie, 'selfie.jpeg')

      const URL = "https://comparafoto-tech.herokuapp.com/compara";
      const data = new FormData();
      
      data.append(`foto_1`, selfie);
      data.append(`foto_2`, this.foto);

      const config = {
        header: {
          "accept": "application/json",
          "Content-Type": "multipart/form-data",
        },
      };

      let res = await axios.post(URL, data, config);

      console.log(res.data);
      this.resultado = res.data

    },

    dataURLtoFile(dataurl, filename) {
 
        var arr = dataurl.split(','),
            mime = arr[0].match(/:(.*?);/)[1],
            bstr = atob(arr[1]), 
            n = bstr.length, 
            u8arr = new Uint8Array(n);
            
        while(n--){
            u8arr[n] = bstr.charCodeAt(n);
        }
        
        return new File([u8arr], filename, {type:mime});
    },

    onFileChange(e) {
      this.foto = e.target.files[0];
      this.url_foto = URL.createObjectURL(this.foto);

    }

  },

}
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.cabecalho {

  display: flex;
  justify-content: center;
  align-items: center;
  color:#0066FF;
  font-weight: bold;
  font-size:x-large;
}

.camera-wrapper {
  width: 100%;
  height: 100%;
  align-items: center;
  background-color: #F2F4FF;
}
.dimensions {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.camera-panel {
  display: flex;
  justify-content: center;
  align-items: center;
}

.button {
    border:none;
    padding:8px 15px;
    border-radius:20px;
    background:#0066FF;
    color:#fff;
    margin: 8px;
    cursor: pointer;
  }

.button_img {
    border:none;
    padding:8px 15px;
    border-radius:20px;
    background:#F2F4FF;
    margin: 8px;
    cursor: pointer;
  }

.img {
  border: 1px solid #ddd;
  border-radius: 4px;
  padding: 5px;
  width: 250px;
  height: 250px;
}

</style>

