<template lang="pug">
    .home
        video.video(ref="video")
        canvas.canvas(ref="canvas")
        .button-wrapper
            button.js-button(data-type="start" @click="init") 起動
            template(v-if="isDebug")
                button.js-button(data-type="shutter" @click="setCanvas") シャッター
                a.js-button(data-type="download" :href="imageURL" target="_blank" download="image") ダウンロード
                button.js-button(data-type="upload" @click="upload") アップロード
</template>

<script>
// @ is an alias to /src
import * as firebase from "firebase";
const fileName = "hogehoge.jpg";

export default {
  name: "home",
  components: {},
  data: function() {
    return {
      media: null,
      video: null,
      context: null,
      width: null,
      height: null,
      canvas: null,
      database: null,
      picture: null,
      targetRef: null,
      isReadyCamera: false,
      isDebug: false
    };
  },
  computed: {
    imageURL() {
      return this.picture;
    }
  },
  methods: {
    init() {
      this.media = navigator.mediaDevices.getUserMedia({
        audio: false,
        video: true
      });

      this.media
        .then(stream => {
          this.video = this.$refs.video;
          this.video.srcObject = stream;
          this.video.onloadedmetadata = () => {
            this.video.play();
          };

          this.isReadyCamera = true;
        })
        .catch(e => {
          throw new Error(e);
        });
    },
    setCanvas(callback) {
      this.canvas = this.$refs.canvas;
      this.canvas.width = this.$refs.video.clientWidth;
      this.canvas.height = this.$refs.video.clientHeight;
      this.context = this.canvas.getContext("2d");
      this.context.drawImage(this.video, 0, 0, this.canvas.width, this.canvas.height);
      this.canvas.toBlob(
        blob => {
          this.picture = blob;
          if (callback instanceof Function) {
            callback();
          }
        },
        "image/jpeg",
        0.5
      );
    },
    upload: async function() {
      const snapshot = await this.targetRef.put(this.picture);
      const downloadURL = await snapshot.ref.getDownloadURL();
      this.database.ref("jidoribachiURL").set({
        url: downloadURL
      });
    },
    saveImageInStorage() {
      this.setCanvas(this.upload.bind(this));
    }
  },
  created: function() {
    // setup strage
    const storage = firebase.storage();
    const rootRef = storage.ref();
    this.targetRef = rootRef.child(fileName);
  },
  mounted: function() {
    this.width = window.innerWidth;
    this.height = window.innerHeight;

    // setup database
    this.database = firebase.database();
    this.database.ref("jidoribachi").on("value", ss => {
      if (ss.val() !== "" && this.isReadyCamera) {
        this.saveImageInStorage();
      }
    });
  }
};
</script>

<style  lang="scss">
canvas {
  position: absolute;
  top: 0;
  left: 0;
  opacity: 0;
}
video {
  width: 100%;
  height: 100%;
}
</style>
