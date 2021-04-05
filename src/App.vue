<template>
  <el-container>
    <el-header class="app-header">
      <el-avatar src="olesya.jpg"></el-avatar>
      <div class="el-col-24 app-title">Говорит Олеся</div>
    </el-header>
    <el-container>
      <el-aside width="200px">
        <el-menu
          class="el-menu-vertical-demo"
          >
          <el-menu-item index="1" @click="SelectText">
            <i class="el-icon-edit-outline"></i>
            <span>Текст</span>
          </el-menu-item>
          <el-menu-item index="2" @click="SelectFile">
            <i class="el-icon-document"></i>
            <span>Документ</span>
          </el-menu-item>
          <el-menu-item index="3" @click="SelectImage">
            <i class="el-icon-picture-outline"></i>
            <span>Изображение</span>
          </el-menu-item>
        </el-menu>
      </el-aside>
      <el-main>
        <div v-if="pagetext">
          <el-form ref="form" :model="formText" label-width="220px" @submit.native.prevent>
            <el-form-item label="Введите текст">
              <el-input type="textarea" v-model="formText.text"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="SendText">Отправить</el-button>
              <el-button @click="formText.text = ''">Отмена</el-button>
            </el-form-item>
          </el-form>
        </div>
        <div v-if="pagefile">
          <el-form ref="form" :model="formFile" label-width="280px" @submit.native.prevent>
            <el-form-item>
              <input class="el-button" type="file" ref="file" v-on:change="handleFileUpload()"/>
            </el-form-item>
            <el-form-item label="Описание документа (<200сим.)">
              <el-input type="text" v-model="formFile.description"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="SendFile">Отправить</el-button>
              <el-button @click="ClearFileData">Отмена</el-button>
            </el-form-item>
          </el-form>
        </div>
        <div v-if="pageimage">Image</div>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import MainPage from './components/MainPage';
import axios from 'axios'

export default {
  name: 'App',

  components: {
   MainPage,
  },

  data: () => ({
    //
    olesyakey:'1333442954:AAEA2Zn5jkvRc3ag6Cv8qdJyX2Bo8G3Fb68',
    chat_id:'-443484708',
    pagetext:true,
    pagefile:false,
    pageimage:false,
    formText:{
      text:''
    },
    formFile:{
      description:'',
      document:''
    }
  }),
  methods:{
    SelectText(){
      this.pagetext = true
      this.pagefile = false
      this.pageimage = false
    },
    SelectFile(){
      this.pagetext = false
      this.pagefile = true
      this.pageimage = false
    },
    SelectImage(){
      this.pagetext = false
      this.pagefile = false
      this.pageimage = true
    },
    SendText(){
      let urltext = `https://api.telegram.org/bot${this.olesyakey}/sendMessage?chat_id=${this.chat_id}&text=${this.formText.text}`
      axios.get(urltext).then(resp=>{
        this.$notify({
          title: 'Олеся сообщает',
          message: 'Текст успешно отправлен',
          type: 'success'
        });
      }).catch(err=>{

      })
    },
    SendFile(){
      let urlfile = `https://api.telegram.org/bot${this.olesyakey}/sendDocument`
      let formData = new FormData();
      formData.append('document',this.formFile.document)
      formData.append('chat_id',this.chat_id)
      formData.append('caption',this.formFile.description)
      axios.post(urlfile,formData,{headers:{'Content-Type': 'multipart/form-data'}}).then(resp=>{
        this.$notify({
          title: 'Олеся сообщает',
          message: 'Документ успешно отправлен',
          type: 'success'
        });
      }).catch(err=>{

      })
    },
    ClearFileData(){
      this.formFile.description = ''
    },
    handleFileUpload(){
      this.formFile.document = this.$refs.file.files[0]
    }
  }
};
</script>
<style >
  body {
    font-family: "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", "微软雅黑", Arial, sans-serif;
    margin: 0px;
  }
  .app-header{
    display: flex;
    align-items: center;
    background-color: aliceblue;

  }
  .app-title{
    margin-left: 0.5rem;
  }
</style>
