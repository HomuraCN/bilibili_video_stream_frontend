<template>

  <el-card id="downloadCard">
    <div>
      <div>
        <el-input
            v-model="url"
            style="max-width: 800px"
            placeholder="Video Url"
        >
          <template #prepend>
            <p style="width: 100px">Url</p>
          </template>
        </el-input>
      </div>

      <div>
        <el-input
            v-model="fileName"
            style="max-width: 800px"
            placeholder="File Name"
        >
          <template #prepend>
            <p style="width: 100px">FileName</p>
          </template>
        </el-input>
      </div>

      <div>
        <el-button type="primary" @click="downloadVideo()">Download</el-button>
      </div>

      <div>
        <el-progress :percentage="progress" :text-inside="true" :stroke-width="20">
          <span>{{ this.currentFileName }}: {{progress}}%</span>
        </el-progress>
      </div>
    </div>
  </el-card>
  <div style="height: 1000px"></div>
</template>

<script>
import axios from "axios";
import { ElMessage } from 'element-plus'

axios.defaults.timeout = 5000;
// 允许跨域
axios.defaults.withCredentials = true;
// Content-Type 响应头
axios.defaults.headers.post["Content-Type"] =
    "application/x-www-form-urlencoded;charset=UTF-8";
// 基础url
axios.defaults.baseURL = '/api'; //注意此处一定要和vite设置的请求名一致

export default {
  name: "VideoDownload",
  components: {},

  data(){
    return{
      url: "",
      fileName: "",
      currentFileName: "",
      progress: 0,
    };
  },
  mounted() {
    this.downloadProgressSocket()
  },
  methods: {
    downloadVideo(){
      axios.get("/downloadVideoWBI?url=" + this.url + "&fileName=" + this.fileName, {

      }).then(res => {
        ElMessage({
          message: 'Download finished.',
          type: 'success',
        })
      })
    },
    downloadProgressSocket(){
      const socket = new WebSocket('ws://localhost:9961/progress');
      socket.onmessage = (event) => {
        const data = JSON.parse(event.data);
        this.currentFileName = data.fileName
        this.progress = data.progress
      }
    }
  }
}

</script>

<style scoped>
div{
  margin-bottom: 10px;
}
#downloadCard{
  margin: 0 auto;
}
</style>