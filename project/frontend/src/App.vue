<template>
  <div class="root_container">
    <div class="search_container">
      <div class="search_comment_first">의료기관 검색</div>
      <div class="search_comment_second">현재 위치에서 가까운 병원을 찾아보세요</div>
      <input class="search" placeholder="진료과를 검색하세요"></input>
    </div>

    <div class="keyword_container">
      <div v-for="(nameItem, i) in name" :key="i" class="keyword">
        <div class="keyword_icon_container">
          <div class="keyword_icon">
            <i :class="nameItem.image"></i>
          </div>
        </div>
        <div class="keyword_info_container">
          <div class="keyword_title">{{ nameItem.title }}</div>
          <div class="keyword_content">{{ nameItem.content }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios';

export default {
  name : 'App',
  data() {
    return {
      name : [],
    }
  },
  methods : {
    async fetch() {
      try{
        const res = await axios.get('http://localhost:8888/');
        this.name = res.data;
      }catch(err) {
        console.error('에러 발생 : ', err);
      }
    }
  },
  mounted() {
    this.fetch();
  }
}
</script>

<style>
@font-face {
    font-family: 'GmarketSansMedium';
    src: url('https://fastly.jsdelivr.net/gh/projectnoonnu/noonfonts_2001@1.1/GmarketSansMedium.woff') format('woff');
    font-weight: normal;
    font-style: normal;
}



body {
  font-family: 'GmarketSansMedium';
  display: flex;
  justify-content: center;
  background: #EFFBEF;
  margin: 0;
  padding: 0;
  color: #253140;
}

div {
  line-height: 1;
}

input {
  all: unset;
}

.root_container {
  border-radius: 30px 30px 0 0;
  box-shadow: rgba(0, 0, 0, 0.34) 2.8px 2.8px 7.7px;
  margin-top: 50px;
  /* border: 1px solid black; */
}
/* search section */
.search_container{
  display: flex;
  flex-direction: column;
  gap: 30px;
  border-radius: 30px 30px 0 0;
  padding: 60px;
  background: linear-gradient(87deg, rgba(20,184,166,1) 0%, rgba(16,185,129,1) 100%);
}

.search_comment_first {
  font-size: 50px;
  color: white;
}

.search_comment_second {
  font-size: 25px;
  color: white;
}

.search {
  display: flex;
  align-items: center;
  width: 1000px;
  height: 50px;
  padding:10px;
  padding-left: 30px;
  font-size: 30px;
  box-shadow: 0 3px 3px rgba(0,0,0,0.2);
  border-radius: 30px;
  background-color: white;
}

/* keyword section */
.keyword_container {
  padding: 60px;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 100px);
  gap: 20px;
  background: white;
}

.keyword {
  display: flex;
  align-items: center;
  padding: 10px;
  cursor: pointer;
  font-size: 30px;
  border-radius: 10px;
  gap: 20px;
  border: 1px solid #F2F2F2;
  box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
}

.keyword:hover {
  background: #F2F2F2;

  .keyword_icon {
    background: #D8D8D8;
  }
}

.keyword_info_container {
  display: flex;
  flex-direction: column;
  height: 60px;
}

.keyword_icon {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 60px;
  height: 60px;
  border-radius: 10px;
  background: #F2F2F2;
}

.keyword_content {
  color: gray;
  font-size: 15px;
}

.keyword_title {
  flex: 1;
}
</style>
