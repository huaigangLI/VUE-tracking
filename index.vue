<template>
  <div class="container">
   <video id="video" preload autoplay loop muted></video>
   <!-- 侦查划线 -->
    <canvas id="canvas" width="600" height="600"></canvas> 
    
    <canvas id="canvas1" width="600" height="600"></canvas>
    <p @click="aaa" style="position: absolute;
    bottom: -77px;
    font-size: 20px;">点击启动</p>
     <span><i class="el-icon-close" style="position: absolute;
    bottom: -180px;
    font-size: 20px;" @click="closeDialog"></i></span>
     <p @click="aaa" style="position: absolute;
    bottom: -120px;
    font-size: 20px;">{{text1}}</p>
  </div>
</template>

<script>
  require('tracking/build/tracking-min.js')
  require('tracking/build/data/face-min.js')
  require('tracking/build/data/mouth-min.js')
  require('tracking/examples/assets/stats.min.js')

  export default {
    name:'testTracking',
    data(){
      return {
        trackerTask:'',
        tracker:{},
        mediaStreamTrack:{},
        text1:'11111111111111111111111',
        video:{},
        count: 0,
        isgoon:true
      }
    },
    methods:{
      aaa () {
        const conts = this
        this.video = document.getElementById('video');
        var canvas = document.getElementById('canvas');
        var context = canvas.getContext('2d');
        conts.tracker = new tracking.ObjectTracker('face');
        conts.tracker.setInitialScale(4);
        conts.tracker.setStepSize(2);
        conts.tracker.setEdgesDensity(0.1);
         conts.openCamera()
        conts.trackerTask = tracking.track('#video', conts.tracker)
          conts.tracker.on('track', function (event) {
            context.clearRect(0, 0, canvas.width, canvas.height);
            event.data.forEach(function (rect) {
                context.strokeStyle = '#fff';
                context.strokeRect(rect.x, rect.y, rect.width, rect.height);
                context.font = '11px Helvetica';
                context.fillStyle = "#fff";
            });
        if(conts.isgoon){
             if ((event.data.length >0) && conts.count <= 5) {
          if (conts.count < 0) conts.count = 0
          conts.count += 1
          if (conts.count > 5) {
            conts.isgoon = false
            conts.text1 = '已检测到人脸，正在登录'
          }
        } else {
          conts.count -= 1
          if (conts.count < 0) conts.text1 = '请您保持脸部在画面中央'
        }
        }
        });

        var canvas1 = document.getElementById('canvas1');
        var context1 = canvas1.getContext('2d');
        context1.strokeStyle = "#69fff1";
        context1.moveTo(190, 118);
        context1.lineTo(390, 118);
        context1.lineTo(390, 318);
        context1.lineTo(190, 318);
        context1.lineTo(190, 118);
        context1.stroke();
      },
        openCamera(){
      if (navigator.mediaDevices === undefined) {
            navigator.mediaDevices = {};
        }
          if (navigator.mediaDevices.getUserMedia === undefined) {
            navigator.mediaDevices.getUserMedia = function (constraints) {
                // 首先，如果有getUserMedia的话，就获得它
                var getUserMedia = navigator.getUserMedia||navigator.webkitGetUserMedia||navigator.mozGetUserMedia||navigator.msGetUserMedia;
                // 一些浏览器根本没实现它 - 那么就返回一个error到promise的reject来保持一个统一的接口
                if (!getUserMedia) {
                    return Promise.reject(new Error('getUserMedia 浏览器不支持摄像头'));
                }

                // 否则，为老的navigator.getUserMedia方法包裹一个Promise
                return new Promise(function (resolve, reject) {
                    getUserMedia.call(navigator, constraints, resolve, reject);
                });
            }
        }
            const constraints = {
            video: true,
            audio: false
        };
        let promise = navigator.mediaDevices.getUserMedia(constraints);
        promise.then(stream => {
      this.mediaStreamTrack = stream.getTracks()[0];
      window.stream = stream
            let video = document.getElementById('video');
        // 旧的浏览器可能没有srcObject
        if ("srcObject" in video) {
            video.srcObject = stream;
        } else {
            // 防止再新的浏览器里使用它，应为它已经不再支持了
            video.src = window.URL.createObjectURL(stream);
        }
        video.onloadedmetadata = function (e) {
            video.play();
        };
        this.realTimeClData  = setInterval(this.takePhoto, 500)
    }).catch(err => {
            console.error(err.name + ": " + err.message);
            console.error("权限失败");
    })
    },
    closeTring(){
      clearInterval(this.timeInterval)
      if (typeof window.stream === 'object') {
        this.video.srcObject = null
        // window.stream.getTracks().forEach(track => track.stop())
      }
    },
        closeDialog(){
         //给父组件传参 关闭进程
            // this.tracker.stop()
              this.trackerTask.stop()
          this.mediaStreamTrack && this.mediaStreamTrack.stop();
            this.$router.replace({ path: '/main' })
          //  this.trackerTask.closeCamera()
        
            clearInterval(this.timeInterval)
      if (typeof window.stream === 'object') {
        this.video.srcObject = null
        // window.stream.getTracks().forEach(track => track.stop())
      }
        }
  },
   created(){
     
    },
  }
</script>

<style  scoped>

        video, #canvas,#canvas1 {
            position: absolute;
            width: 600px;
            height: 600px;
        }
        #canvas1 {
            position: absolute;
        }
        .container {
            position: relative;
            width: 581px;
            height: 436px;
        }
</style>
