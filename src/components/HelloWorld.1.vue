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
      <div class="draw" id="draw">
        <ul @touchstart.stop.prevent="touchstart" @touchmove.stop.prevent="touchmove" @touchend.stop.prevent="touchend">
          <li v-for="item in linkList" :key="item.id">{{item.content}}</li>
        </ul>
      </div>
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
      timer:null,
      linkList:[
        {id:1,content:'催发货'},
        {id:2,content:'修改收货地址'},
        {id:3,content:'服务中心查询'},
        {id:4,content:'人工客服'},
        {id:5,content:'在线服务'},
      ]
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
    this.setScroll()
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
    setScroll(){
      var draw = document.querySelector('#draw');
    var ul = draw.children[0];
    var ulLen = ul.children.length
    var width = 0
    for(var i = 0; i < ulLen;i++) {
      console.log(ul.children[i].offsetWidth)
      width += ul.children[i].offsetWidth
    }
    ul.style.width = width + ulLen*4+'px'
    var startX = 0; // 刚触碰到屏幕的时的手指信息
    var centerX = 0; // 用来记录每次触摸时上一次的偏移距离
    var maxRight = 50; // 设定一个最大向右滑动的距离
    var maxLeft = -(ul.offsetWidth - draw.offsetWidth + maxRight); // 求得一个最大向左滑动的距离
    var maxLeftBounce = 0; // 向左反弹值
    var maxRightBounce = -(ul.offsetWidth - draw.offsetWidth); // 向右反弹值

    // touchstart 时，记录手指在 Y 轴上的落点距离可视顶部距离
    ul.addEventListener('touchstart', function (e) {
      startX = e.changedTouches[0].clientX;
    })

    // touchmove 时，记录此时手指在 Y 轴上的落点距离可视顶部距离
    ul.addEventListener('touchmove', function (e) {
      // 清除过渡
      ul.style.transition = 'none';
      // 获取差值
      var dx = e.changedTouches[0].clientX - startX;

      // 上次的滑动距离加上本次的滑动距离
      var tempX = centerX + dx;

      // 当上次滑动的距离加上本次滑动的距离 大于 设定的最大向下距离的时候
      if (tempX > maxRight) {
        tempX = maxRight;
      }
      // 当上次滑动的距离加上本次滑动的距离 小于 设定的最大向上距离的时候 
      else if (tempX < maxLeft) {
        tempX = maxLeft;
      }

      // 设置 ul 在 Y 轴上的偏移
      ul.style.transform = 'translateX(' + tempX + 'px)';
    })

    // touchend 时，记录此时手指在 Y 轴上的落点距离可视顶部距离
    ul.addEventListener('touchend', function (e) {
      // 获取差值
      var dx = e.changedTouches[0].clientX - startX;
      // 记录移动的距离
      centerX = centerX + dx;

      // 两次滑动的距离 大于 设定的 向上 反弹值时
      if (centerX > maxLeftBounce) {
        // 让两次滑动的距离 等于 设置的值
        centerX = maxLeftBounce;
        // 添加过渡
        ul.style.transition = 'transform .5s';
        ul.style.transform = 'translateX(' + centerX + 'px)';
      }
      // 两次滑动的距离 小于 设定的 向下 反弹值时
      else if (centerX < maxRightBounce) {
        // 让两次滑动的距离 等于 设置的值
        centerX = maxRightBounce;
        // 添加过渡
        ul.style.transition = 'transform .5s';
        ul.style.transform = 'translateX(' + centerX + 'px)';
      }
    })
    }
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
}
.draw li {
  list-style: none;
  float: left;
  height: 100%;
  text-align: center;
  padding: 10px 10px;
  border: 1px solid #ccc;
  margin-right: 4px;
}
</style>
