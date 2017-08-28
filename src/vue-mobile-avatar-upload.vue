<template>
  <div ref="main" v-show='show' class="vue-mobile-avatar-upload"  v-on:touchmove.stop.prevent="touch">
    <div class="photo-body-outer">
      <input v-show='false' type="file" @change="handleChange" ref="fileinput" accept="image/*">
      <div  v-show="!showCanvas"  ref="imgback" class="old-img-back">
        <div :style="imgStyle" ref='imgouter' class="old-img-outer">
          <img ref="oldImg" class="old-img-img" :src='sourceImgUrl' @load="imgInit"/>
          <transition>
            <div ref='imgmask'  class="mask-img">
            </div>
          </transition>
        </div>
      </div>
      <div v-show="showCanvas" class="old-canvas-back">
        <canvas class='canvas' :width="screenWidth" :height='screenWidth' ref="canvas"></canvas>
      </div>
      <div v-show="!showCanvas" class="bottom">
        <span @click='back' class="left">{{tips.cancel}}</span>
        <span @click='crop' class="right">{{tips.crop}}</span>
      </div>
      <div v-show="showCanvas" class="bottom">
        <span @click='back' class="left">{{tips.cancel}}</span>
        <span @click='upload' class="right">{{tips.upload}}</span>
      </div>
    </div>
    <div v-if="uploading" class="uploading-outer">
        <div class="uploading-box">
          <img src="data:image/png;base64,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" />
          <div class="uploading-tip">{{tips.uploading}}</div>
        </div>
    </div>
  </div>
</template>
<script type="text/javascript">
  import EXIF from './exif.js';
  import languagePackage from './language';
  export default{
    name:'photo-crop-uploader',
    props:{
      //图片上传地址
      action: {
        type: String,
        required: true
      },
      maxSize:{
        type:Number,
        required:false,
        default:6000
      },
      language:{
        type:String,
        required:false,
        default:'en'
      }
    },
    data(){
      let lan=languagePackage[this.language];
      return{
        show:false,
        imgInfor:'',
        sourceImgUrl:'',
        screenHeight:'',
        screenWidth:'',
        maskStyle:{
          left:0,
          top:0,
          width: 100,
          height:100,
        },
        imgSize:{
          width:0,
          height:0,
        },
        realSize:{
          width:0,
          height:0,
        },
        canvasStyle:{
          width:"100px",
          height:'100px',
        },
        maskScale:1,
        maskDiagonal:0,
        showCanvas:false,
        cropInfor:{
            top:0,
            bottom:0,
            left:0,
            right:0,
        },
        test:true,
        uploading:false,
        tips:lan,
      }
    },
    computed:{
      maskStyleReal(){
        let temp={};
        for(let i in this.maskStyle){
          temp[i]=this.maskStyle[i]+'px';
        }
        return temp;
      },
      imgStyle(){
        let temp={
          height:"auto",
          width:'auto',
        };
        if(this.imgSize.width==0||this.imgSize.height==0){
          return temp;
        }
        temp.left=(this.screenWidth-this.imgSize.width)/2+'px';
        temp.top=(this.$refs.imgback.offsetHeight-this.imgSize.height)/2+'px';
        temp.height=this.imgSize.height+'px';
        temp.width=this.imgSize.width+'px';
        return temp;
      }
    },
    watch:{
      show(val){
        if(!val){
          this.imgSize.width=0;
          this.imgSize.height=0;
          this.showCanvas=false;
          this.sourceImgUrl='';
          this.$refs.fileinput.value="";
        }
      }
    },
    created(){
      this.screenHeight=window.screen.height;
      this.screenWidth=window.screen.width;
    },
    methods:{
      back(){
        if(this.showCanvas){
          this.showCanvas=false;
        }else{
          this.show=false;
        }
      },
      handleClick() {
        this.$refs.fileinput.click();
      },
      handleChange(e) {
        this.show=true;
        let that = this;
        e.preventDefault();
        let files = e.target.files || e.dataTransfer.files;
        if (this.checkFile(files[0])) {
          this.setSourceImg(files[0]);
        }
        EXIF.getData(files[0], function(img) {
          that.imgInfor=EXIF.getTag(files[0],'Orientation');
        });
      },
      checkFile(file) {
        if (file.type.indexOf('image') === -1) {
          console.warn('请选择图片！');
          return false;
        }
        if (file.size / 1024 > this.maxSize) {
          console.warn('图片过大！');
          return false;
        }
        return true;
      },
      // 设置图片源
      setSourceImg(file) {
        let that = this,
          fr = new FileReader();
        fr.onload = function(e) {
          that.sourceImgUrl = fr.result;
          that.getHW(fr.result);
        }
        fr.readAsDataURL(file);
      },
      getHW(val){
        let newImg=new Image();
        let that=this;
        newImg.onload=function(){
          that.realSize.width=newImg.width;
          that.realSize.height=newImg.height;
        }
        newImg.src=val;
      },
      imgInit(){
        let temph=this.$refs.imgouter.offsetHeight;
        let tempw=this.$refs.imgouter.offsetWidth;
        if(temph>=this.$refs.imgback.offsetHeight||(temph/tempw>(this.$refs.imgback.offsetHeight)/this.screenWidth)){
          this.imgSize.height=this.$refs.imgback.offsetHeight;
          this.imgSize.width=tempw*this.imgSize.height/temph;
        }else{
          this.imgSize.width=this.screenWidth;
          this.imgSize.height=temph*this.imgSize.width/tempw;
        }
        if(this.imgSize.width>=this.imgSize.height){
          this.maskStyle.height=this.imgSize.height;
          this.maskStyle.width=this.imgSize.height;
        }else{
          this.maskStyle.height=this.imgSize.width;
          this.maskStyle.width=this.imgSize.width;
        }
        this.maskStyle.left=0;
        this.maskStyle.top=0;
        this.border();
      },
      touch(ev){
        this.test=!this.test;
        if(!this.test){
          return;
        }
        if(ev.touches.length==1){
          this.onMove(ev.touches[0].clientX,ev.touches[0].clientY);
        }else if(ev.touches.length==2){
          this.onZoom(ev.touches[0],ev.touches[1])
        }
      },
      onMove(x,y){
        let left=(x-this.maskStyle.width/2)-parseFloat(this.imgStyle.left);
        let top=(y-this.maskStyle.height/2)-parseFloat(this.imgStyle.top)-44;
        if(left>=0&&(left+this.maskStyle.width)<this.imgSize.width){
          this.maskStyle.left=left;
        }else if(left<0&&this.maskStyle.left>0){
          this.maskStyle.left=0;
        }else if((left+this.maskStyle.width)>this.imgSize.width&&(this.maskStyle.left+this.maskStyle.width)<this.imgSize.width){
          this.maskStyle.left=this.imgSize.width-this.maskStyle.width;
        }
        let tempImgSize=this.imgSize.height;
        if(top>=0&&(top+this.maskStyle.height)<tempImgSize){
          this.maskStyle.top=top;
        }else if(top<0&&this.maskStyle.top>0){
          this.maskStyle.top=0;
        }else if((top+this.maskStyle.height)>this.imgSize.height&&(this.maskStyle.top+this.maskStyle.height)<this.imgSize.height){
          this.maskStyle.top=this.imgSize.height-this.maskStyle.height;
        }
        this.border();
      },
      onZoom(finger1,finger2){
        let temp=Math.pow(finger2.clientY-finger1.clientY,2)+Math.pow(finger2.clientX-finger1.clientX,2)
        if(this.maskScale==1){
          this.maskScale=1.0001;
          this.maskDiagonal=Math.sqrt(temp);
        }else{
          this.maskScale=Math.sqrt(temp)/this.maskDiagonal;
          this.maskDiagonal=Math.sqrt(temp);
        }

        let tempW=this.maskStyle.width;
        let tempH=this.maskStyle.height;

        if(this.maskScale>1&&(this.maskStyle.width>=this.imgSize.width||this.maskStyle.height>=this.imgSize.height)){
          this.maskScale=0.9999;
          return
        }

        this.maskStyle.width=this.maskStyle.width*this.maskScale;
        this.maskStyle.height=this.maskStyle.height*this.maskScale;

        if(this.maskStyle.left>=0||this.maskScale<1) {
          if(this.maskStyle.left+this.maskStyle.width<this.imgSize.width){
            this.maskStyle.left = this.maskStyle.left + (tempW - this.maskStyle.width) / 2;
          }else{
            this.maskStyle.left = this.maskStyle.left + (tempW - this.maskStyle.width);
          }

        }
        if(this.maskStyle.top>=0||this.maskScale<1) {
          if(this.maskStyle.top+this.maskStyle.height<this.imgSize.height) {
            this.maskStyle.top = this.maskStyle.top + (tempH - this.maskStyle.height) / 2;
          }else{
            this.maskStyle.top = this.maskStyle.top + (tempH - this.maskStyle.height);
          }
        }
        this.border()
      },
      border(){
        this.getimgStyle();
        this.cropInfor.left=this.maskStyle.left;
        this.cropInfor.top=this.maskStyle.top;
        this.cropInfor.right=this.imgSize.width-this.maskStyle.left-this.maskStyle.width;
        this.cropInfor.bottom=this.imgSize.height-this.maskStyle.top-this.maskStyle.height;
      },
      getimgStyle(){
        for(let i in this.maskStyle){
          this.$refs.imgmask.style[i]=this.maskStyle[i]+'px';
        }
      },
      crop(){
        this.showCanvas=true;
        let canvas = this.$refs.canvas;
        let ctx = canvas.getContext('2d');
        ctx.clearRect(0, 0, this.screenWidth,this.screenWidth);
        this.rotate(ctx);
      },
      rotate(ctx){
        let rate=this.realSize.width/this.imgSize.width;
          if(this.imgInfor != "" && this.imgInfor != 1){
            switch(this.imgInfor){
              case 6://需要顺时针（向左）90度旋转
                ctx.save();
                ctx.translate(this.screenWidth,0);
                ctx.rotate(Math.PI / 2);
                ctx.drawImage(this.$refs.oldImg, this.cropInfor.top*rate*3/4,this.cropInfor.right*rate*3/4, this.maskStyle.height*rate*3/4, this.maskStyle.width*rate*3/4,0, 0, this.screenWidth,this.screenWidth);
                ctx.restore();
                break;
              case 8://需要逆时针（向右）90度旋转
                ctx.save();
                ctx.translate(0,this.screenWidth);
                ctx.rotate(3*Math.PI / 2);
                ctx.drawImage(this.$refs.oldImg, this.cropInfor.bottom*rate*3/4,this.cropInfor.left*rate*3/4, this.maskStyle.width*rate*3/4, this.maskStyle.height*rate*3/4,0, 0, this.screenWidth,this.screenWidth);
                ctx.restore();
                break;
              case 3://需要180度旋转
                ctx.save();
                ctx.translate(this.screenWidth,this.screenWidth);
                ctx.rotate(Math.PI);
                ctx.drawImage(this.$refs.oldImg, this.cropInfor.right*rate, this.cropInfor.bottom*rate, this.maskStyle.width*rate, this.maskStyle.height*rate,0, 0, this.screenWidth,this.screenWidth);
                ctx.restore();
                break;
              default:
                ctx.drawImage(this.$refs.oldImg, this.maskStyle.left*rate, this.maskStyle.top*rate, this.maskStyle.width*rate, this.maskStyle.height*rate,0, 0, this.screenWidth,this.screenWidth);
            }
          }else{
            ctx.drawImage(this.$refs.oldImg, this.maskStyle.left*rate, this.maskStyle.top*rate, this.maskStyle.width*rate, this.maskStyle.height*rate,0, 0, this.screenWidth,this.screenWidth);
          }

      },
      upload(){
        this.$emit('onBegin');
        this.uploading=true;
        let canvas = this.$refs.canvas;
        let dataurl = canvas.toDataURL("image/png").substring(23);
        let formData = new FormData();
        formData.append('image',dataurl);
        let xhr =new XMLHttpRequest();
        xhr.open('post',this.action);
        xhr.onreadystatechange=()=>{
          if(xhr.readyState == 4){
            if(xhr.status >= 200&&xhr.status<300||xhr.status==304) {
              let response = xhr.responseText;
              this.show = false;
              this.$emit('onSuccess', response);
            }else{
              this.$emit('onError', xhr.status);
            }
            this.uploading=false;

          }
        };
        xhr.send(formData);
      }
    }
  }
</script>
<style  scoped rel="stylesheet/css">
  .vue-mobile-avatar-upload{
    position: fixed;
    width: 100%;
    height:100%;
    top:0px;
    left:0px;
    background-color:#000;
    z-index: 2000;
  }
  .vue-mobile-avatar-upload .photo-body-outer{
    padding:44px 0px;
    height:100%;
    width:100%;
    position:relative;
    box-sizing:border-box;
  }
  .vue-mobile-avatar-upload .old-img-back{
    height:100%;
    width:100%;
    position:relative;
  }
  .vue-mobile-avatar-upload .old-img-outer{
    max-width:100%;
    overflow: hidden;
    position: absolute;
  }
  .vue-mobile-avatar-upload .old-img-img{
    width:100%;
    display: block;
  }
  .vue-mobile-avatar-upload .mask-img{
    position: absolute;
    border-color: rgba(0,0,0,0.5);
    border-style: solid;
    border-width:1000px;
    box-sizing:content-box;
    margin:-1000px;
  }
  .vue-mobile-avatar-upload .old-canvas-back{
    height:100%;
    width:100%;
    position:relative;
    display: flex;
    flex-direction:column;
    justify-content:center;
    align-items:center;
  }
  .vue-mobile-avatar-upload .canvas{
    background-color:#fff;
  }
  .vue-mobile-avatar-upload .bottom{
    width:100%; height:44px; background: #414141; color:#fff; position: fixed; bottom:0px; left:0px;

  }
  .vue-mobile-avatar-upload .bottom  .left, .vue-mobile-avatar-upload .bottom .right{
    position: absolute; top:0px; height:44px; line-height:44px; padding:0px 20px;
  }
  .vue-mobile-avatar-upload .bottom .left{
    left:0px;
  }
  .vue-mobile-avatar-upload .bottom .right{
    right:0px;
  }
  .vue-mobile-avatar-upload .uploading-outer{
    position:fixed;
    top:0px;
    bottom:0px;
    left:0px;
    right:0px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  .vue-mobile-avatar-upload .uploading-box{
    padding-top:16px;
    width:80px;
    height:90px;
    border-radius: 6px;
    background-color:rgba(0,0,0,0.6);
    flex:0 0 auto;
    text-align:center;
    line-height:26px;
  }
  .vue-mobile-avatar-upload .uploading-box img{
    width:40px;
    animation:myrotate 2s linear infinite;
  }
  .vue-mobile-avatar-upload .uploading-tip{
    color:#fff;
    font-size:14px;
  }
  @keyframes myrotate
  {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
</style>
