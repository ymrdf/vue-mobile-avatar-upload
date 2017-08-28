<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Click on the button below to use</h2>
    <div @click="openUploader" class="button-sub">upload avatar</div>
    <photo-crop-uploader language="zh"
                         ref='photo'
                         @onSuccess="uploadSuccess"
                         action="http://192.168.11.254:8060/zhsq/phone/basetopic/convert2">

    </photo-crop-uploader>
  </div>
</template>

<script>
import photoCropUploader from 'vue-mobile-avatar-upload'
//  import photoCropUploader from './myphoto/photo.vue'
export default {
  components: {
    photoCropUploader
  },
  name: 'hello',
  data () {
    return {
      msg: 'Welcome to use vue-avatar-crop-uploader'
    }
  },
  methods: {
    openUploader () {
      this.$refs.photo.handleClick()
    },
    uploadSuccess () {

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
.button-sub{
  box-sizing: border-box;
  text-align: center;
  overflow: hidden;
  color:#fff;
  background: #57B382;
  font-size:24px;
  height:44px;
  line-height:44px;
}
</style>
