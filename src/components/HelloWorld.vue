<template>
  <div>
    <el-card v-if="visable" style="">
      <h1>人脸识别demo</h1>
      <p><i class="el-icon-refresh"></i>提示：模型最初校验与重新校验时可能会稍慢，请耐心等候，或刷新界面重启</p>
    </el-card>
      <el-card>
        <div id="box">
          <div class="box1">
            <video id="video" width="400" height="300" playsinline loop></video>
            <canvas id="canvas" width="400" height="300"></canvas>
          </div>
          <div class="box3">
            <h1>摄像头<i class="el-icon-loading"></i></h1>
          </div>
          <div class="box3">
            <h1><i class="el-icon-loading"></i>模型</h1>
          </div>
        <div class="box2">
            <canvas id="model" width="400" height="300"></canvas>
          </div>
     </div>
      </el-card>

      <el-card>
        <el-tag type="primary">
          点击获取脸部坐标按钮，将打印其中10个点于下列表中:
        </el-tag>
        <p id="positions"></p>
        <div class="button-container">
          <el-button type="success" id="live" @click="opemCam()">开启摄像头</el-button>
          <el-button type="warning" id="liveEnd">关闭摄像头</el-button>
          <el-button type="primary" id="snap">脸部跟踪开启</el-button>
          <el-button type="primary" id="clmStart">获取脸部坐标</el-button>
          <el-button type="info" id="console" @click="consoleData()">控制台输出一组数据</el-button>
        </div>
      </el-card>
  </div>
</template>

<script>
import clm from 'clmtrackr'
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      video: '',
      canvas: '',
      ctx: '',
      ctracker: '',
      visable: true
    }
  },
  mounted() {
    this.initVideo()
  },
  methods: {
    initVideo() {
      this.video = document.getElementById('video');
      this.canvas = document.getElementById('canvas');
      console.log(this.video)
      console.log(this.canvas)
      this.ctx = canvas.getContext('2d');   //参数 contextID 指定了您想要在画布上绘制的类型。当前唯一的合法值是 "2d"
      var width = video.width;  
      var height = video.height;  
      this.canvas.width = width;  
      this.canvas.height = height;
      const _this = this
      document.getElementById("live").addEventListener('click',function(){  
        _this.liveVideo(width, height);
        _this.initClmTrackr();
      });
    },
    liveVideo(width, height) {
      console.log('1111')
      console.log(video)
      const _this = this
      var URL = window.URL || window.webkitURL;   // 获取到window.URL对象
        navigator.getUserMedia({  
          video: true  
        }, function(stream){  
          video.src = URL.createObjectURL(stream);   // 将获取到的视频流对象转换为地址
          video.play();   // 播放
          //点击截图     
          document.getElementById("snap").addEventListener('click', function() { 
            function drawLoop() {
              requestAnimationFrame(drawLoop); 
              _this.ctx.clearRect(0, 0, canvas.width, canvas.height);
              _this.ctracker.draw(canvas);
              var model = document.getElementById("model")
              var ctx2 = model.getContext('2d');
              ctx2.clearRect(0, 0, model.width, model.height);
              _this.ctracker.draw(model);
            }
            drawLoop();
          });
          document.getElementById("liveEnd").addEventListener('click',function(){
          video.pause();
                  console.log(stream)
                  stream.getVideoTracks()[0].stop();
                  video.src = "";
          });
        }, function(error){  
          console.log(error.name || error);  
        });
    },
    initClmTrackr() {
      const _this = this
      var ctracker = new clm.tracker();
      this.ctracker = ctracker
      ctracker.init();
      ctracker.start(this.video);
      console.log('ctrackr');
      console.log(ctracker);
      document.getElementById("clmStart").addEventListener('click',function(){  
        console.log('开始跟踪了');
        function positionLoop() {
					requestAnimationFrame(positionLoop);
					var positions = ctracker.getCurrentPosition();
					// do something with the positions ...
					// print the positions
					var positionString = "";
					if (positions) {
						for (var p = 0;p < 10;p++) {
							positionString += "featurepoint "+p+" : ["+positions[p][0].toFixed(2)+","+positions[p][1].toFixed(2)+"]<br/>";
						}
						document.getElementById('positions').innerHTML = positionString;
					}
				}
				positionLoop();
      });
    },
    consoleData() {
      const _this = this
      function positionLoop() {
					// requestAnimationFrame(positionLoop);
					var positions = _this.ctracker.getCurrentPosition();
          console.log(positions)
					// do something with the positions ...
					// print the positions
					var positionString = "";
					if (positions) {
						for (var p = 0;p < 10;p++) {
							positionString += "featurepoint "+p+" : ["+positions[p][0].toFixed(2)+","+positions[p][1].toFixed(2)+"]<br/>";
						}
						document.getElementById('positions').innerHTML = positionString;
					}
				}
				positionLoop();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#box {
  width: 100%;
  height: 350px;
  display: flex;
}
.box1 {
  position: relative;
  width: 50%;
  flex: 4;
}
.box3 {
  flex: 1;
  text-align: center;
  line-height: 300px;
}
.box2 {
  position: relative;
  width: 50%;
  flex: 4;
}
#canvas {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  border: 1px solid #ccc;
  border-radius: 20px;
}
#model {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  border: 1px solid #000000;
  border-radius: 20px;
  color: #fff;
  background: #000;
}
#video {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  border: 1px solid #333;
  border-radius: 20px;
  background: #666;
}
.button-container {

}
hiddenCard{
  visibility: hidden;
}
</style>
