<template>
  <div class="root_container">
    <div class="search_container">
      <vue3-tags-input :tags="tags" placeholder="진료과를 검색 하세요." @on-tags-changed="handleChangeTag" />
    </div>

    <div id="map"></div>

    <div class="tag_container">
      <div class="tag" v-for="(tagItem, i) in tagList" :key="i" @click="handleAddTag(tagItem)"> {{ tagItem }}</div>
    </div>

    <div class="keyword_container">
      <div class="hospital_info" v-for="(hospitals, i) in hospitalList" :key="i">
        <div class="hospital_flex" @click="link">
          <div class="hospital_container">
            <div class="hospital_name"> {{ hospitals.hospitalName }} </div>
            <div class="hospital_department"> {{ hospitals.subject }} </div>
            <div class="hospital_address"> {{ hospitals.hospitalAddress }} </div>
            <div class="hospital_content"> {{ hospitals.hospitalAddress }} </div>
          </div>
          <img class="hospital_img" :src="hospitalimg">
        </div>
        <div class="hr"></div>
      </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios';
import hospitalList from '@/assets/hospitalData.js';
import hospitalimg from '@/assets/hospital.png';
import { setUserLocation } from '@/services/setUserLocation';
import Vue3TagsInput from 'vue3-tags-input';

export default {
  name: 'About',
  components: {
    Vue3TagsInput
  },
  data() {
    return {
      map: null,
      tagList: ['응급실', '전문의', '24시간', '야간진료', '주차 가능', '응급실', '전문의', '24시간', '야간진료', '전문의', '전문의', '전문의', '24시간', '야간진료',],
      hospitalList: hospitalList,
      hospitalimg: hospitalimg,
      radius: 1.0,
      tags: [this.$store.getters.department],
    }
  },

  mounted() {
    this.fetch();

    if (window.kakao && window.kakao.maps) {
      this.loadMap();
    } else {
      this.loadScript();
    }
  },
  methods: {
    handleChangeTag(tags) {
      this.tags = tags;
    },

    handleAddTag(tags) {
      this.tags.push(tags);
    },

    async fetch() {
      try {
        const res = await axios.get('http://localhost:8889/hospital_main/mapData', {
          params: {
            sub: this.$route.params.title,
            userLat: this.$store.getters.userLat,
            userLng: this.$store.getters.userLng,
            radius: this.radius
          }
        });

        this.hospitalList = res.data;
        if (this.map) {
          this.loadMaker();
        }
      } catch (err) {
        console.error('에러 발생 : ', err);
      }
    },

    // api 불러오기
    loadScript() {
      const script = document.createElement("script");
      script.src = "";
      script.onload = () => window.kakao.maps.load(this.loadMap);

      document.head.appendChild(script);
    },

    // 맵 출력
    loadMap() {
      const container = document.getElementById("map");
      const options = {
        center: new window.kakao.maps.LatLng(this.$store.getters.userLat, this.$store.getters.userLng),
        level: 3
      }

      this.map = new window.kakao.maps.Map(container, options);
      this.loadUserMaker();
      this.loadMaker();
      this.loadCircle();
    },

    // 사용자 마커 불러오기
    loadUserMaker() {
      const imageSrc = 'https://t1.daumcdn.net/localimg/localimages/07/mapapidoc/marker_red.png';
      const imageSize = '64, 69';
      const iamgeOption = { offset: new kakao.maps.Point(27, 69) };
      const markerImage = new window.kakao.maps.MarkerImage(imageSrc, imageSize, iamgeOption);
      const markerPosition = new window.kakao.maps.LatLng(this.$store.getters.userLat, this.$store.getters.userLng);

      const marker = new window.kakao.maps.Marker({
        position: markerPosition,
        title: '현재 위치',
        image: markerImage,
      });

      marker.setMap(this.map);

    },

    // 병원 마커 불러오기
    loadMaker() {
      this.hospitalList.forEach(hospital => {
        // const imageSrc = 'https://t1.daumcdn.net/localimg/localimages/07/mapapidoc/marker_red.png';
        // const imageSize = '64, 69';
        // const iamgeOption = {offset: new kakao.maps.Point(27, 69)};
        // const markerImage = new window.kakao.maps.MarkerImage(imageSrc, imageSize, iamgeOption);
        const markerPosition = new window.kakao.maps.LatLng(hospital.coordinateY, hospital.coordinateX);

        const marker = new window.kakao.maps.Marker({
          position: markerPosition,
          title: hospital.hospitalName,
          // image: markerImage,
        });

        marker.setMap(this.map);
        const customOverlay = this.loadCustomOverlay(hospital.coordinateY, hospital.coordinateX, hospital.hospitalName);

        window.kakao.maps.event.addListener(marker, 'click', () => {
          customOverlay.setMap(this.map);
        });

      });
    },

    // 커스텀 오버레이
    loadCustomOverlay(y, x, name) {
      var content = '<div class="customoverlay">' +
        '  <a href="http://www.kidshealth.co.kr/" target="_blank">' +
        '    <span class="title">' + name + '</span>' +
        '  </a>' +
        '</div>';

      var position = new window.kakao.maps.LatLng(y, x);
      var customOverlay = new window.kakao.maps.CustomOverlay({
        map: null,
        position: position,
        content: content,
        yAnchor: 1
      });

      return customOverlay;
    },

    // 반경 표시
    loadCircle() {
      var circle = new window.kakao.maps.Circle({
        center: new window.kakao.maps.LatLng(this.$store.getters.userLat, this.$store.getters.userLng),
        radius: this.radius * 1000, // 미터 단위의 원의 반지름 
        strokeWeight: 5, // 선 두께 
        strokeColor: '#75B8FA', // 선 색깔
        strokeOpacity: 1, // 선의 불투명도, 1에서 0 사이의 값이며 0에 가까울수록 투명
        strokeStyle: 'dashed', // 선 스타일
        // fillColor: '#CFE7FF', // 채우기 색깔
        // fillOpacity: 0.1  // 채우기 불투명도   
      })
      circle.setMap(this.map);
    },

    link() {
      window.location.href = 'http://www.kidshealth.co.kr/';
    },
  },
}
</script>

<style scoped>
.search_container {
  padding: 30px 60px;
}

.search_input {
  position: relative;
}

/* .search {
  width: 100%;
} */

.search_tag {
  position: absolute;
  border-radius: 25px;
  padding: 5px 30px;
  cursor: pointer;
  background-color: #11BF7F;
  color: white;
  top: 50%;
  transform: translateY(-50%);
  box-shadow: rgba(50, 50, 93, 0.25) 0px 6px 12px -2px, rgba(0, 0, 0, 0.3) 0px 3px 7px -3px;
}

.search_tag:hover {
  color: white;
  background: #11BF7F;
}

#map {
  height: 40vh;
}

.tag_container {
  padding: 15px 0;
  margin: 0 10px;
  display: flex;
  gap: 15px;
  overflow-x: auto;
  white-space: nowrap;
  /* justify-content: center; */
}

.tag {
  margin-left: 4px;
  display: flex;
  flex: 0 0 auto;
  background: #F5F6F7;
  /* color: white; */
  padding: 10px 20px;
  border-radius: 25px;
  cursor: pointer;
  box-shadow: rgba(50, 50, 93, 0.25) 0px 6px 12px -2px, rgba(0, 0, 0, 0.3) 0px 3px 7px -3px;
  /* box-shadow: rgba(99, 99, 99, 0.2) 0px 2px 8px 0px; */
  /* box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px; */
}

.tag:hover {
  color: white;
  background: #11BF7F;
}

.keyword_container {
  padding: 0 10px;
  padding-bottom: 15px;
  display: grid;
  grid-template-columns: repeat(1, 1fr);
  grid-auto-rows: 160px;
  gap: 15px;
  background: white;
}

.hospital_info {
  display: flex;
  flex-direction: column;
}

.hospital_container {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.hospital_name {
  /* color: #298A08; */
  font-size: 20px;
  /* background-color: aqua; */
}

.hospital_department {
  color: gray;
  /* background-color: beige; */
}

.hospital_address {
  color: gray;
  /* background-color: tomato; */
}

/* .hospital_content {
  color: #298A08;
  background-color: yellow;
} */

.hospital_flex {
  display: flex;
  justify-content: space-between;
  cursor: pointer;
}

.hospital_img {
  width: 200px;
  height: 100%;
  border-radius: 10px;
}

.hr {
  width: 100%;
  height: 1px;
  margin-top: auto;
  background-color: #E6E6E6;
}
</style>