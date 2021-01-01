<template>
  <div class="flex-container">
    <div class="ws-tree" ref="wsTree"></div>
    <div class="ws-split" ref="wsSplit" @mousedown="handleMouseDown"></div>
    <div class="ws-main"></div>
  </div>
</template>
<script>
export default {
  name: 'flexLayout',
  data() {
    return {
      dragging: false,
    }
  },
  methods: {
    handleMouseDown(event) {
      const wsSplit = this.$refs.wsSplit;
      const columnRect = wsSplit.getBoundingClientRect();
      const startX = event.clientX;

      document.onselectstart = function() { return false; };
      document.ondragstart = function() { return false; };

      const handleMouseMove = event => {
        let endX = event.clientX;
        let width = columnRect.left + (endX - startX);
        this.$refs.wsTree.style.width = width + 'px';
        return false;
      }

      const handleMouseUp = () => {
        document.body.style.cursor = '';
        document.removeEventListener('mousemove', handleMouseMove);
        document.removeEventListener('mouseup', handleMouseUp);
        document.onselectstart = null;
        document.ondragstart = null;
        wsSplit.releaseCapture && wsSplit.releaseCapture();
      }

      document.addEventListener('mousemove', handleMouseMove);
      document.addEventListener('mouseup', handleMouseUp);

      wsSplit.setCapture && wsSplit.setCapture(); //该函数在属于当前线程的指定窗口里设置鼠标捕获 
      return false;
    }
  }
}
</script>
<style lang='less'>
html,
body,
#app {
  height: 100%;
}
.flex-container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: row;
  position: relative;
  .ws-tree {
    width: 300px;
    min-width: 300px;
    max-width: 50%;
    height: 100%;
    background-color: #ececec;
  }
  .ws-split {
    width: 2px;
    height: 100%;
    background-color: #ccc;
    cursor: col-resize;
    &:hover {
      background-color: #6d70dc;
    }
  }
  .ws-main {
    flex: 1 1 auto;
    background-color: #e4d7d7;
  }
}
</style>