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
      <div class="draw" id="draw" ref="draw">
        <ul @touchstart="touchstart" @touchmove="touchmove" @touchend="touchend">
          <li v-for="item in linkList" :key="item.id" @click="click(item.content)">{{item.content}}</li>
        </ul>
      </div>
      <input type="text" placeholder="点击提问" v-model="issue" >
      <i @click.stop="askQestion"></i>
    </div>
  </div>
</template>

<script>
export default {
  name: 'iscroll',
  data () {
    return {
      clientHeight: null,
      showHeight:null,
      chatContent:[
        {'text':"您好请问有什么可以帮您?", 'flag':'reply'},
        {'text':"我那个货现在还可以换颜色吗", 'flag':'request'},
      ],
      issue:'',
      timer:null,
      linkList:[
        {id:1,content:'催发货'},
        {id:2,content:'修改收货地址'},
        {id:3,content:'服务中心查询'},
        {id:4,content:'人工客服'},
        {id:5,content:'在线服务'},
        {id:6,content:'以旧换新'},
        {id:7,content:'优享购'},
        {id:8,content:'体验店查询'},
        {id:9,content:'修改发票'},
      ],
      startX:0,
      centerX:0,
      maxRight:50,
      maxLeft:0,
      maxLeftBounce:0,
      maxRightBounce:0,
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
    var _this = this
    this.initHeight()
    this.$nextTick(()=>{
      var container = this.$el.querySelector("#chatBoxes");
      container.scrollTop = container.scrollHeight;
    })
    // window.onload = function(){
      _this.setUlWidth()
    // }
    // document.getElementById("body").addEventListener("touchmove", function (e) {
    //   e.preventDefault();
    // });
    var myScroll = new IScroll('#draw',{scrollX:true,scrollY:false});
    console.log(myScroll)
  },
  methods: {
    click(text){
      
      var requesParams = {'text':text, flag:'request'}
      this.chatContent.push(requesParams)
    },
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
    touchstart($event){
      console.log(1)
       
      this.startX = $event.changedTouches[0].clientX;
    },
    touchmove($event) {
      console.log(2)
      var ul = this.$refs.draw.children[0]
       // 清除过渡
      ul.style.transition = 'none';
      // 获取差值
      var dx = $event.changedTouches[0].clientX - this.startX;
      // 上次的滑动距离加上本次的滑动距离
      var tempX = this.centerX + dx;
      // 当上次滑动的距离加上本次滑动的距离 大于 设定的最大向下距离的时候
      if (tempX > this.maxRight) {
        tempX = this.maxRight;
      }
      // 当上次滑动的距离加上本次滑动的距离 小于 设定的最大向上距离的时候 
      else if (tempX < this.maxLeft) {
        tempX = this.maxLeft;
      } 
      // 设置 ul 在 Y 轴上的偏移
      ul.style.transform = 'translateX(' + tempX + 'px)';
    },
    touchend($event){
      console.log(3)
      var ul = this.$refs.draw.children[0]
      // 获取差值
      var dx = $event.changedTouches[0].clientX - this.startX;
      // 记录移动的距离
      this.centerX = this.centerX + dx;

      // 两次滑动的距离 大于 设定的 向上 反弹值时
      if (this.centerX > this.maxLeftBounce) {
        // 让两次滑动的距离 等于 设置的值
        this.centerX = this.maxLeftBounce;
        // 添加过渡
        ul.style.transition = 'transform .1s';
        ul.style.transform = 'translateX(' + this.centerX + 'px)';
      }
      // 两次滑动的距离 小于 设定的 向下 反弹值时
      else if (this.centerX < this.maxRightBounce) {
        // 让两次滑动的距离 等于 设置的值
        this.centerX = this.maxRightBounce;
        // 添加过渡
        ul.style.transition = 'transform .1s';
        ul.style.transform = 'translateX(' + this.centerX + 'px)';
      }
    },
    setUlWidth(){
      var draw = this.$refs.draw;
      var ul = draw.children[0];
      var ulLen = ul.children.length
      var width = 0
      for(var i = 0; i < ulLen;i++) {
        width += ul.children[i].offsetWidth
      }
      ul.style.width = width + (ulLen)*4+'px'
      this.maxLeft = -(ul.offsetWidth - draw.offsetWidth + this.maxRight); // 求得一个最大向左滑动的距离
      this.maxLeftBounce = 0; // 向左反弹值
      this.maxRightBounce = -(ul.offsetWidth - draw.offsetWidth); // 向右反弹值
    }
  }
}
</script>


<style scoped>
* {
  margin: 0;
  padding: 0;
  touch-action: pan-y;
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
  height: 94px;
  background-color: #fff;
  padding: 0 8%;
  box-sizing: border-box;
}
.userInput input[type='text']{
  width: 90%;
  height: 40px;
  outline: none;
  border:none;
  font-size: 16px;
  padding: 0 2%;
}
.userInput i {
  position: absolute;
  left: 88%;
  top:60px;
  width: 24px;
  height: 24px;
  background:url(../assets/icon.png);
}
.draw {
  width: 100%;
  height: 50px;
  border: 2px solid #ccc;
  overflow: hidden;
}
.draw ul:after {
  content: "";
  display: block;
  visibility: hidden;
  height: 0;
  clear: both;
}
.draw ul {
  zoom: 1;
  height: 100%;
  -webkit-overflow-scrolling:touch;
}
.draw li {
  list-style: none;
  float: left;
  height: 30%;
  text-align: center;
  padding: 10px 10px;
  border: 1px solid #ccc;
  margin-right: 4px;
}
.draw ul li:last-child {
  margin-right: 0;
}
</style>
