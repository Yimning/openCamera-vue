<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-redpacket_fill"></i> js调用摄像头拍照上传图片
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div align="center">
                <p id="flag" class="tishi"></p>
            </div>

            <el-button type="primary" icon="el-icon-camera-solid" @click="openCamera">点击打开</el-button>
            <el-button type="primary" icon="el-icon-camera-solid" @click="closeCamera">关闭</el-button>
            <!--图片展示-->
            <!--确认-->
            <el-button type="primary" icon="el-icon-camera-solid" @click="photograph">拍照</el-button>

            <div>
                <!--开启摄像头-->
                <!-- <img @click="openCamera" :src="headImgSrc" alt="摄像头" /> -->
                <video ref="video" width="480px" height="400px" autoplay="autoplay"></video>

                <!--canvas截取流-->
                <canvas ref="canvas" width="480px" height="400px" style="display: none;"></canvas>
            </div>
        </div>
    </div>
</template> 

<script>
export default {
    data() {
        return {
            headImgSrc: '',
            flag: false
        };
    },
    methods: {
        // 调用摄像头
        openCamera() {
            // H5调用电脑摄像头API
            navigator.mediaDevices
                .getUserMedia({
                    video: true
                })
                .then((success) => {
                    // 摄像头开启成功
                    this.$refs['video'].srcObject = success;
                    // 实时拍照效果
                    this.$refs['video'].play();
                })
                .catch((error) => {
                    console.error('摄像头开启失败，请检查摄像头是否可用！');
                });
            setTimeout(() => {  
                //设置延迟执行
                this.photograph();  //5秒后自动执行一次
                this.$refs['video'].load();
              //  console.log('---------------------');
            }, 5000);
             this.$refs['video'].load();
        },
        test() {
            console.log(this.$refs.video);
        },
        // 拍照
        photograph() {
            let ctx = this.$refs['canvas'].getContext('2d');
            // 把当前视频帧内容渲染到canvas上
            ctx.drawImage(this.$refs['video'], 0, 0, 640, 480);
            // 转base64格式、图片格式转换、图片质量压缩
            let imgBase64 = this.$refs['canvas'].toDataURL('image/jpeg', 0.7); // 由字节转换为KB 判断大小

            let str = imgBase64.replace('data:image/jpeg;base64,', '');
            console.log(str);
            let strLength = str.length;
            let fileLength = parseInt(strLength - (strLength / 8) * 2); // 图片尺寸  用于判断
            let size = (fileLength / 1024).toFixed(2);
            console.log(size); // 上传拍照信息  调用接口上传图片 .........

            // 保存到本地
            let ADOM = document.createElement('a');
            ADOM.href = this.headImgSrc;
            ADOM.download = new Date().getTime() + '.jpeg';
            ADOM.click();
        },
        // 关闭摄像头
        closeCamera() {
            if (!this.$refs['video'].srcObject) return;
            let stream = this.$refs['video'].srcObject;
            let tracks = stream.getTracks();
            tracks.forEach((track) => {
                track.stop();
            });
            this.$refs['video'].srcObject = null;
        }
    }
};
</script>

<style>
.getface {
    position: absolute;
    top: 20%;
    left: 35%;
    box-shadow: 0 0px 12px 0 rgba(0, 0, 0, 0.1);
}
.tishi {
    font-size: 20px;
}
</style>



