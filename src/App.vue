<template>
  <!-- {{LocationObject.lat}} -->
  <div class="m-0 p-0 w-screen h-screen" id="map"></div>
  <!-- <div @click="panTo()">이동</div> -->
  <div><Dalseo :locationObject="LocationObject" @passloc="passloc($event); panTo($event)"
          :isDalseoMenuOpen="dalseomenu" @menuopen="menuopen()"  /></div>
</template>

<script>
  import Dalseo from './components/Dalseo.vue';

  export default {
  data() {
    return {
      // 챗지피티에서 백엔드 나누고 이용한것처럼 하면 mykey를 숨길수 있을까?
      mykey: "2c48253f67eaa4f8aa65098574b666ac",
      // 왼쪽 시장 메뉴창 상태
      dalseomenu: false,
      LocationObject: {}
    };
  },
  components: {
    Dalseo
  },
  mounted() {
    if (window.kakao && window.kakao.maps) {
      this.initMap();
    } else {
      const script = document.createElement("script");
      /* global kakao */
      script.onload = () => kakao.maps.load(this.initMap);
      script.src =
        `//dapi.kakao.com/v2/maps/sdk.js?autoload=false&appkey=${this.mykey}`;
      document.head.appendChild(script);
    }
  },
  methods: {
    initMap() {
      const container = document.getElementById("map");
      const options = { //지도를 생성할 때 필요한 기본 옵션
        center: new kakao.maps.LatLng(35.82937620632367, 128.53256501704902),
        level: 6, //지도의 레벨(확대, 축소 정도)
      }; 

      //지도 객체를 등록합니다.
      //지도 객체는 반응형 관리 대상이 아니므로 initMap에서 선언합니다.
      this.map = new kakao.maps.Map(container, options);
    },

    panTo(infoloc){
      // 이동할 위도 경도 위치를 생성합니다
      var moveLatLon = new kakao.maps.LatLng(infoloc.lat, infoloc.lot);
  
      // 지도 중심을 부드럽게 이동시킵니다
      // 만약 이동할 거리가 지도 화면보다 크면 부드러운 효과 없이 이동합니다
      this.map.panTo(moveLatLon);
    },
    menuopen() {
      this.dalseomenu? this.dalseomenu = false : this.dalseomenu = true
    },
    passloc(infoloc){
      // 추후에 push로 배열추가 하여 여러개 선택 가능하도록 하기
      this.LocationObject = infoloc
      // 마커가 표시될 위치입니다 

      // infoloc.으로 가는게 좋을까 아니면 LocationObject. 으로 접근하는게 좋을까 고민해보기

      // var markerPosition  = new kakao.maps.LatLng(infoloc.lat, infoloc.lot); 
      var markerPosition  = new kakao.maps.LatLng(this.LocationObject.lat, this.LocationObject.lot); 
      // 마커를 생성합니다
      var marker = new kakao.maps.Marker({
          position: markerPosition
      });
      // 마커가 지도 위에 표시되도록 설정합니다
      marker.setMap(this.map);
      // 아래 코드는 지도 위의 마커를 제거하는 코드입니다
      // marker.setMap(null);
    },
  },
};
</script >
