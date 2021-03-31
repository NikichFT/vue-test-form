<template>
  <div class="wrap">
    <Modal v-if="modal" @closeModal="modal = false"/>
    <div class="form-title">Форма подачи заявки в отдел сервиса и качества</div>
    <div class="form">
      <div class="form__item">
        <div class="item-title">Ваш филиал<div class="star">*</div></div>
        <select name="city" id="city" :disabled="online" v-model="city" :class="{disabled: online}">
          <option value disabled selected>Выберите город</option>
          <option v-for="city in cities" :value="city.title" :key="city.id">{{city.title}}</option>
        </select>
        <br>
        <input @change="disableCityChange" type="checkbox" name="" id="check" v-model="online">
        <label for="check">Онлайн</label>
      </div>
      <div class="form__item">
        <div class="item-title">Тема обращения<div class="star">*</div></div>
        <form>
          <div class="group">
            <input type="radio" @click="otherTopic = ''" id="one" name="choose" value="Недоволен качеством услуг" v-model="topic">
            <label for="one">Недоволен качеством услуг</label>
          </div>
          <br>
          <div class="group">
            <input type="radio" @click="otherTopic = ''" id="two" name="choose" value="Расторжение договора" v-model="topic">
            <label for="two">Расторжение договора</label>
          </div>
          <br>
          <div class="group">
            <input type="radio" @click="otherTopic = ''" id="three" name="choose" value="Не приходит письмо активации на почту" v-model="topic">
            <label for="three">Не приходит письмо активации на почту</label>
          </div>
          <br>
          <div class="group">
            <input type="radio" @click="otherTopic = ''" id="four" name="choose" value="Не работает личный кабинет" v-model="topic">
            <label for="four">Не работает личный кабинет</label>
          </div>
        </form>
          <input type="text" @keyup="topicOther" v-model="otherTopic" class="input-other" placeholder=" Другое" >
      </div>  
      <div class="form__item">
        <div class="item-title">Описание проблемы<div class="star">*</div></div>
        <textarea type="text" v-model="description" class="input-description" placeholder="Введите текст"></textarea>
      </div> 
      <div class="form__item">
        <div class="item-title">Загрузка документов</div>
        <div class="file-des" for="file">Приложите, пожалуйста, полноэкранный скриншот. <br> Это поможет быстрее решить проблему.</div>
        <input type="file" ref="file" @change="fileHandler" id="file" name="file">
      </div>
      <button class="send" :disabled="!required" @mouseup="sendPost" :class="{disabled: !required}">ОТПРАВИТЬ</button> 
    </div>
  </div>
</template>

<script>
import Modal from './Modal.vue'
export default {
  name: 'Form',
  components: {
    Modal
  },
  data() {
    return {
      city: '',
      online: false,
      topic: '',
      otherTopic: '',
      description: '',
      file: '',
      required: false,
      success: null,
      modal: false,
      urlCities: 'https://60254fac36244d001797bfe8.mockapi.io/api/v1/city',
      urlPost: 'https://60254fac36244d001797bfe8.mockapi.io/api/v1/send-form',
      cities: []
    }
  },
  methods: {
    disableCityChange(){
      this.city = ''
    },
    topicOther(){
      this.topic = this.otherTopic;
    },
    fileHandler(){
      let formData = new FormData()
      let selectedFile = this.$refs.file.files[0]
      formData.append("selectedFile", selectedFile)
      this.file = formData
    },
    requestSender(method, url, body=null){
      new Promise((resolve, reject) => {
      
      const request = new XMLHttpRequest()

      request.open(method, url)
      request.setRequestHeader('Content-type', 'application/json')
      request.onload = () => {
        request.status >= 400 ? reject(request.response) : resolve(request.response)
      }

      request.onerror = () => {
        reject(request.response)
      }

      request.send(JSON.stringify(body))
    })
    .then(result => {
      if (method === 'GET') {
      const data = JSON.parse(result)
      this.cities.push(...data)}
      if (method === 'POST') {
        const data = JSON.parse(result)
        if (data.success) {this.city = '', this.online = false, this.topic = '', this.description = '', this.file = '', this.otherTopic = ''}
        else {alert('Ошибка отправки заявки')}
        this.success = data.success
        this.modal = data.success
      }
      })
    .catch(error => console.log(error))
    },
    sendPost(){
      const userReq = {
        city: this.city,
        online: this.online,
        topic: this.topic,
        description: this.description,
        file: this.file
      }
      this.requestSender('POST', this.urlPost, userReq)
    }
  },
  mounted(){
    return this.requestSender('GET', this.urlCities)
  },
  updated(){
    return this.required = (this.online || this.city) && this.topic && this.description ? true : false
  }
}
</script>


<style scoped lang="scss">

%margin-blocks{
  margin-bottom: 10px;
}
@mixin font-title{
  font-family: 'Rubik', sans-serif;
}
@mixin font-text{
  font-family: 'Noto Sans', sans-serif;
}
label{
  cursor: pointer;
  font-size: 0.8em;
}
.input-other, select{
  border: 1px solid #ccc;
  width: 200px;
  height: 30px;
  padding: 0 0 0 5px;
  @extend %margin-blocks;
  color: rgb(90, 90, 90);
}
select{
  cursor: pointer;
  width: 205px;
}
input[type=radio]{
  visibility: hidden;
  position: absolute;
}
input[type=radio] + label::before, input[type=checkbox]{
  content: " ";
  display:inline-block;
  vertical-align: middle;
  height: 20px;
  width: 20px;
  margin: 0 10px 0 0;
  border: 1px solid #000;
  cursor: pointer;
}
input[type=radio] + label::before{
  border-radius:50%;
}
input[type=radio]:checked + label::before {
  background: radial-gradient(circle, rgba(0,0,0,1) 40%, rgba(255,255,255,0.4066001400560224) 42%);
}
input[type=checkbox]{
  height: 20px;
  width: 20px;
  appearance: none;
  border: 1px solid #000;
  border-radius: 2px;
}
input[type=checkbox]::after{
  content: '';
  position: absolute;
  width: 12px;
  height: 8px;
  background: rgba(0, 0, 0, 0);
  border: 2px solid #000000;
  border-top: none;
  border-right: none;
  vertical-align: middle;
  -webkit-transform: rotate(-45deg);
  -moz-transform: rotate(-45deg);
  -o-transform: rotate(-45deg);
  -ms-transform: rotate(-45deg);
  transform: rotate(-45deg);
  display: none;
}
input[type=checkbox]:checked::after{
  display: block;
  margin-left: 2px;
}
.group{
  display: flex;
  align-items: center;
  @extend %margin-blocks;
}
.input-description{
  height: 10vh;
  width: 97%;
  vertical-align: baseline;
  resize: none;
  @include font-text;
  padding-left: 5px;
}
.form{
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  min-height: 80vh;
  border: 2px solid #ebebeb;
  border-radius: 5px;
  padding: 3%;
}
.form-title{
  @include font-title;
  font-size: 1.5em;
  margin-bottom: 20px;
}
.star{
  color: red;
  margin-left: 5px
}
.item-title{
  font-size: 1em;
  @include font-text;
  margin-bottom: 10px;
  display: flex;
}
.file-des{
  @extend %margin-blocks;
  font-size: 0.8em;
  color: rgb(121, 121, 121);
}
#file{
  @extend %margin-blocks;
}
.send{
  width: 100px;
  height: 30px;
  @include font-text;
  background-color: rgb(241, 138, 3);
  color: #ffffff;
  border: none;
  font-size: 0.7em;
  cursor: pointer;
  &:hover{
    opacity: 0.7;
  }
}

.disabled{
  background-color: rgb(223, 223, 223);
}
</style>
