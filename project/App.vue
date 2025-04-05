<template>

  <div class="black-bg" v-if="modal_open == true">
    <div class="white-bg">
      <h4>상세페이지임</h4>
      <p>상세페이지 내용임</p>
      <button @click="modal_open = false">닫기</button>
    </div>
  </div>

  <div class="menu">
    <a v-for="(name, i) in menus" :key="name">{{ name }}</a>
  </div>

  <div v-for="(info, i) in onerooms" :key="i">
    <img :src="info.image" class="room-img">
    <h4>{{info.title}}</h4>
    <p>{{info.price}}원</p>
  </div>
</template>

<script>

import data from './assets/oneroom.js';


export default {
  name : 'App',
  data() {
    return {
      onerooms : data,
      modal_open : false,
      num : [0,0,0],
      menus : ['Home', 'Shop', 'About'],
      bluecolor : 'color : blue',
      products : ['역삼동원룸', '천호동원룸', '마포구원룸'],
    }
  },
  methods : {
    increase() {
      this.num += 1
    }
  },
}
</script>
<style>
body {
  margin: 0;
}

div {
  box-sizing: border-box;
}

.black-bg {
  width: 100%; height: 100%;
  background: rgba(0, 0, 0, 0, 5);
  position: fixed; padding: 20px;
}

.white-bg {
  width: 100%; background: white;
  border-radius: 8px;
  padding: 20px;
}

.room-img {
  width: 100%;
  margin-top: 40px;
}

#app {
  text-align: center;
}

.menu {
  background: darkslateblue;
  padding: 15px;
  border-radius: 5px;
}

.menu a {
  color: white;
  padding: 10px;
}
</style>
