<template>
  <div class="onlineService" :style="{'height':clientHeight+'px'}">
    <div class="title">
      <h1>在线服务</h1>
    </div>
    <div class="chatBoxes" id="chatBoxes" :style="{'height':(clientHeight)+'px'}">
      <div class="innerBox">
        <div class="commonChat"   v-for="(item,index) in chatContent" :class="index">
          <div v-if="item.flag == 'reply'" class="reply">{{item.text}}</div>
          <div v-if="item.flag == 'request'" class="request">{{item.text}}</div>
        </div>
      </div>
    </div>
    <div class="userInput" >
      <input type="text" placeholder="点击提问" v-model="issue" >
      <i @click.stop="askQestion"></i>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      clientHeight: null,
      showHeight:null,
      chatContent:[
        {'text':"您好请问有什么可以帮您?", 'flag':'reply'},
        {'text':"我那个货现在还可以换颜色吗", 'flag':'request'},
      ],
      issue:'',
      timer:null
    }
  },
  watch: {
    'chatContent':'scrollToBottom',
    clientHeight(newV, oldV){
      this.$nextTick(()=>{
        var container = document.getElementById('chatBoxes')
        container.scrollTop = container.scrollHeight;
      })
    }
  },
  mounted() {   
    this.initHeight()
    this.$nextTick(()=>{
      var container = this.$el.querySelector("#chatBoxes");
      container.scrollTop = container.scrollHeight;
    })
  },
  methods: {
    scrollToBottom() {
      this.$nextTick(()=>{
        var container = document.getElementById('chatBoxes')
        container.scrollTop = container.scrollHeight;
      })
    },
    initHeight(){  //获取高度
      //获取浏览器可视区域高度
      this.clientHeight = document.documentElement.clientHeight
      window.onresize = () => {  //当窗口或框架发生改变时触发
        this.clientHeight = document.documentElement.clientHeight
      }
    },
    askQestion() {
      var requesParams = {'text':this.issue, flag:'request'}
      this.chatContent.push(requesParams)
    },
    // onFocus() {
    //   this.$nextTick(()=>{
    //     var container = document.getElementById('chatBoxes')
    //     console.log(container)
    //     container.scrollTop = container.scrollHeight;
    //   })
    //   // this.timer = setInterval( ()=> {      
    //   //   document.body.scrollTop = document.body.scrollHeight    
    //   // }, 100)
    // },
    // onBlur() {
    //   // clearInterval(this.timer)
    //   // document.body.scrollTop = document.body.scrollHeight
    // }
  }
}
</script>


<style scoped>
* {
  margin: 0;
  padding: 0;
}
.onlineService {
  width: 100%;
  background-color:#CDE1EC
}
.title {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  line-height: 51px;
  color: #fff;
  background-color: blue;
  border-bottom: 1px solid blue;
  text-align: center;
  
}
.title>h1 {
   font-size: 20px;
}
.chatBoxes {
  padding:62px 10px 40px;
  /* margin-top: 52px; */
  /* margin-bottom: 40px; */
  box-sizing: border-box;
  overflow: scroll;
}
.innerBox {
  background-color:#CDE1EC;
}
.commonChat {
  width: 100%;
  padding-bottom: 20px;
  overflow: hidden;
  font-size: 15px;
}
.reply, .request {
  padding: 10px 20px;
  max-width: 80%;
  word-wrap:break-word;
}
.reply {
  float: left;
  border-radius: 20px 20px 20px 4px;
  background-color: #fff;
  color: #000
}
.request {
  float: right;
  border-radius: 20px 20px 4px 20px ;
  background-color: #fff;
  color: #1A8FFF;
}

.userInput {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 40px;
  background-color: #fff;
  padding: 0 8%;
  box-sizing: border-box;
}
.userInput input[type='text']{
  width: 90%;
  height: 100%;
  outline: none;
  border:none;
  font-size: 16px;
  padding: 0 2%;
}
.userInput i {
  position: absolute;
  left: 88%;
  top:8px;
  width: 24px;
  height: 24px;
  background:url(../assets/icon.png);
}
</style>
