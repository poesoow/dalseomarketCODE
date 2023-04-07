<template>
  <div>
    <!-- 각 상점 팝업창 https://tailwindcomponents.com/component/modal-7 에서 가져와서 사용해봄 -->
      <div class="z-50">
      
      <div class="flex items-center justify-center absolute h-screen w-screen top-0 left-0">
      <transition name="custom" enter-active-class="animate__animated animate__bounceInDown" leave-active-class="animate__animated animate__bounceOutUp">
      <!-- Modal -->
      <div v-if="showModal" class="z-[70] relative w-11/12 lg:w-full max-w-xl mx-auto bg-white flex flex-col self-center shadow-2xl rounded-md ">
        <!-- Modal header -->
        <div class="p-6 border-b-4 border-gray-200 text-lg font-bold text-indigo-400">{{ modalStoreData['상점명'] }}</div>
        <!-- ./Modal header -->
    
        <!-- Modal body -->
        <div class="p-6">
          <div>{{ modalStoreData['도로명 주소'] }}</div>
          <div>{{ modalStoreData['지번 주소'] }}</div>
          <div>
            <div class="bg-slate-200" id="roadview" style="width:100%;height:300px;">1</div>
          </div>

        </div>
        <!-- ./Modal body -->
    
        <!-- Modal footer -->
        <div class="border-t-4 border-gray-200 p-6 flex justify-end">
          <button @click="$emit('closeModal') " class="bg-green-400 hover:bg-green-500 focus:outline-none px-4 py-2 rounded-md text-white transition duration-500 ease-in-out">Close Modal</button>
        </div>
        <!-- ./Modal footer -->
      </div>
      <!-- ./Modal -->
      </transition>
  
      <transition name="custom" enter-active-class="animate__animated animate__fadeIn" leave-active-class="animate__animated animate__fadeOut">
        <!-- Overlay -->
        <div v-if="showModal" class="bg-gray-700 bg-opacity-50 fixed bottom-0 left-0 w-full h-full transition duration-500 ease-in-out transfom z-[60]"></div>
        <!-- ./Overlay -->
      </transition>
      </div>
      </div>
  </div>
</template>

<script>
  export default {
    name: 'StoreModal',
    data() {
      return {
        // showModal: false, // https://tailwindcomponents.com/component/modal-7 에서 가져온 값 사용
        // modalStoreData: {}, // 모달용 데이터(모달 클릭 시 해당 상점만 나오는거라서 filter 사용안하고 직접 데이터 지정으로 사용 filter로도 시도해보기)
      }
    },
    props: {
      showModal: Boolean,
      modalStoreData: Object
    },
    methods:{
      // 
      // storeroadmap() {
      //   var roadviewContainer = document.getElementById('roadview'); //로드뷰를 표시할 div
      //   var roadview = new kakao.maps.Roadview(roadviewContainer); //로드뷰 객체
      //   var roadviewClient = new kakao.maps.RoadviewClient(); //좌표로부터 로드뷰 파노ID를 가져올 로드뷰 helper객체

      //   var position = new kakao.maps.LatLng(33.450701, 126.570667);

      //   // 특정 위치의 좌표와 가까운 로드뷰의 panoId를 추출하여 로드뷰를 띄운다.
      //   roadviewClient.getNearestPanoId(position, 50, function (panoId) {
      //     roadview.setPanoId(panoId, position); //panoId와 중심좌표를 통해 로드뷰 실행
      //   });
      // }
    }
  }
</script>

<style>

</style>