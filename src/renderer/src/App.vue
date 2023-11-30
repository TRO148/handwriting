<script setup>
import {onMounted, ref} from "vue";

const tablet = ref();
let ctx = null;
let imageData = null;
onMounted(() => {
  ctx = tablet.value.getContext("2d");
  // 设置线
  ctx.lineWidth = 1;
  ctx.lineCap = 'round';
  ctx.lineJoin = 'round';

  // 鼠标按下
  let prex, prey // 记录上一点的位置
  const mousedown = (e) => {
    imageData = ctx.getImageData(0, 0, tablet.value.width, tablet.value.height);

    prex = e.offsetX
    prey = e.offsetY
    ctx.beginPath();
    ctx.moveTo(prex, prey)
    tablet.value.addEventListener('mousemove', mousemove)
    document.addEventListener('mouseup', mouseup)
    tablet.value.addEventListener('mouseenter', mouseenter)
  }
  const mousemove = (e) => {
    ctx.lineTo(e.offsetX, e.offsetY);
    ctx.stroke();
  }
  const mouseup = () => {
    ctx.closePath();
    tablet.value.removeEventListener('mousemove', mousemove);
    document.removeEventListener('mouseup', mouseup);
    tablet.value.removeEventListener('mouseenter', mouseenter);
  }
  const mouseenter = (e) => {
    prex = e.offsetX;
    prey = e.offsetY;
    ctx.moveTo(prex, prey);
  }
  tablet.value.addEventListener('mousedown', mousedown);
})

const clear = () => {
  ctx.clearRect(0, 0, tablet.value.width, tablet.value.height);
}

const exp = () => {
  const data = tablet.value.toDataURL();
  const a = document.createElement('a');
  a.href = data;
  a.download = 'canvas.png';
  a.click();
}

const color = (e) => {
  ctx.strokeStyle = e.target.value;
}

const back = () => {
  ctx.putImageData(imageData, 0, 0);
}

</script>

<template>
  <div id="page">
    <header>
      <input type="color" @click="color">
      <button @click="back">BACK</button>
      <button @click="clear">CLEAR</button>
      <button @click="exp">EXPORT</button>
    </header>
    <canvas width="960" height="800" id="tablet" ref="tablet"></canvas>
  </div>
</template>

<style>
#page {
  display: flex;
  flex-direction: column;
  justify-content: center;

  gap: 10px;
}

header {
  display: flex;
  flex-direction: row;
  justify-content: center;
  gap: 10px;
}

header button,input {
  border: #ccc 2px dotted;
  background: transparent;

  font-family: "Berlin Sans FB";
  box-shadow: 0 2px 2px rgba(0, 0, 0, 0.5);
}

canvas {
  border: #ccc 8px dashed;
}

#tablet {
  margin: auto;
  background: #eee;
}
</style>
