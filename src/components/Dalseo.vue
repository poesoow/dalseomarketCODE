<template>
  <div>
    <!-- 좌측 시장종류 창 -->
    <div :class="this.isDalseoMenuOpen === true && '!left-4'" class="flex gap-2 flex-row-reverse fixed top-4 -left-[360px] transition-all duration-500 z-50">
    <!-- {{locationObject}} -->
    <!-- {{isDalseoMenuOpen}} -->
      <div @click="$emit('menuopen')" class="w-fit text-blue-800 text-5xl cursor-pointer"><font-awesome-icon icon="fa-shop" /></div>
      <div class="rounded-2xl bg-white w-[360px] h-[95vh] shadow-xl">
        <div class="py-7 px-6 rounded-t-2xl bg-blue-700">
          <h2 class="text-white tracking-wide">
            <img class="inline-block h-[2rem] align-text-bottom" src="@/assets/images/daegu.png" alt="대구로고">
            <span> 대구광역시 달서구 </span>
            <span class="text-2xl">전통시장</span></h2>
          <div class="bg-white mt-4 pt-3 pb-2 px-1 rounded-md">



            <!-- select태그의 id, name, 다시 찾아보기 -->
            <select class="outline-none align-text-top" name="" id="">
              <option value="">상점명</option>
              <option value="">대표품목</option>
            </select>
            <input class="rounded-lg w-3/5 outline-none mx-2 pl-1 placeholder:text-sm focus:outline-none focus:border-blue-600 focus:ring-blue-600 focus:ring-2" type="text" placeholder="상점명을 입력해주세요">
            <button class="align-text-top text-blue-600" ><font-awesome-icon icon="fa-magnifying-glass" /></button>
          </div>
        </div>

        <div class="pt-7 px-6">
          <h2 class="text-xl text-center tracking-wide">전통시장</h2>
          <ul class="mt-4 grid grid-cols-2 gap-3 text-center text-sm">
             <!-- selectedMarket = e.marketName; focusOn = !focusOn; num = index" -->
            <li v-for="(e, index) in itemList" :key="e"
              @click="$emit('passloc', {lat: e.lat , lot: e.lot, marketName: e.marketName }); getMarketInfo(e.marketName); selectMarket(index);"
              :class="focusOn && num === index ? 'text-blue-700 font-bold' : 'text-black'"
              class="border border-blue-100 py-1 hover:font-bold">
              <div>{{ e.marketName }}</div>
            </li>
          </ul>
        </div>
      </div>
    </div>

    <!-- 우측 상점 정보 창 -->
    <div :class="isMarketInfoOpen ? 'right-0' : 'right-[-400px]'" class="bg-white fixed top-0 w-[400px] h-[100vh] z-50 overflow-y-scroll  transition-all duration-500">
      <div class="relative">
        <div class="pt-7 px-6">
          <h2 class="text-xl text-center tracking-wide border-b text-blue-600">상가분류</h2>
          <ul class="flex justify-between py-2">
            <li @click="storeClassify = '전체'">전체</li>
            <li @click="storeClassify = e.storeClassify" v-for="e in storeClassifyList" :key="e" >{{ e.storeClassify }}</li>
          </ul>
        </div>
        <div class="pt-4 px-6">
          <h2 class="text-xl text-center tracking-wide border-b text-blue-600">상가목록</h2>
          <ul class="flex flex-col gap-4" >
            <li class="border" v-for="e in storeClassifyItem" :key="e">
              <h3 class="text-lg">{{ e.storeName }}</h3>
              <div class="text-sm">{{ e.roadNmAddr }}</div>
              <div class="w-[48%] inline-block text-lg">{{ e.mainItem }}</div>
              <div class="w-[48%] inline-block text-right mb-1">
                <!-- Modal trigger -->
                <button 
                  @click="showModal = !showModal; modalStoreData = {'상점명': e.storeName, '도로명 주소': e.roadNmAddr, '지번 주소': e.lotnoAddr, 'lat':e.lat, 'lot':e.lot}"
                  class="bg-indigo-500 hover:bg-indigo-600 focus:outline-none rounded-md px-6 py-0.5 mx-auto text-white transition duration-500 ease-in-out">추가정보 정보</button></div>
            </li>
          </ul>
        </div>
        <button @click="isMarketInfoOpen = false" class="absolute top-1 right-1">닫기</button>
      </div>
    </div>
    <!-- 모달창 // 지도와 Dalseo.vue 에서 받은 데이터를 활용해서 컴포넌트화 시킴 -->
    <StoreModal :showModal="showModal" :modalStoreData="modalStoreData" @closeModal="showModal = false" />

  </div>
</template>

<script>
import axios from 'axios'
import market from '@/assets/MarketName.json'
import StoreModal from '@/components/storeModal.vue'

export default {
  name: 'App',
  data() {
    return {
      dalseomykey: "vZqohu%2F1a1mKILW%2BslUEpMtWGBv0IvvlkE7zB28hKgPXkvzNMi9%2F2sawykKToWPc%2F%2FNeUM9rnQykkPu%2FLdPlpQ%3D%3D",
      pageNo: "1", // 페이지번호 기본값 1
      numOfRows: "100", //한페이지 결과 수 기본값 100 // 0329 현재 총 total 개수가 27개 값이 한페이지에 10개씩 해두었다가 api서버에서 totalcount가 늘어날 경우 알아서 자동으로 늘리게 하고 싶으면 코드를 어떻게 수정해야 할까?? axios로 totalcount 만 먼저 불러오고 그 값을 여기에 넣는 방식을 해보고 싶음
      totalCount: "", //총 개수 서버에서 값이 변경 될수 있어서 "" 선언만 하고 axios 에서 값 넣어주었음
      itemList: [], // itemlist ※ 전체가 아닌 불러온 페이지에 대한 itemlist 주의 현재는 nomofrows 100으로 해서 전체
      market: market, //시장별 음식점 정보 불러오기 위한 시장영어이름 json 파일
      marketitemlist: [], //선택한 시장 정보
      storeClassify: '전체', //선택한 시장 상가분류
      modalStoreData: {}, // 모달용 데이터(모달 클릭 시 해당 상점만 나오는거라서 filter 사용안하고 직접 데이터 지정으로 사용 filter로도 시도해보기)
      // 마켓을 클릭하고 나면 클릭 된 상태 보여주기 위해
      focusOn: false,
      num: null,
      // 레이아웃 용
      isMarketInfoOpen: false, // 레이아웃용 (우측 시장상가정보),
      showModal: false, // https://tailwindcomponents.com/component/modal-7 에서 가져온 값 사용
    }
  },
  props: {
    isDalseoMenuOpen: Boolean, // 레이아웃용 (좌측 시장정보) 
    locationObject: Object, // 시장 위치 전송
  },
  components: {
    StoreModal
  },
  created() {
    axios.get(`https://apis.data.go.kr/3470000/dalseoMarket/viewMarketList?serviceKey=${this.dalseomykey}&pageNo=${this.pageNo}&numOfRows=${this.numOfRows}`)
      .then((res) => {
        // console.log(res.data.body.items.item)
        this.totalCount = res.data.body.totalCount
        this.itemList = res.data.body.items.item
      })
      .catch((error) => {
        console.log(error);
      })
  },
  methods:{
    // focusOn 이 false 인경우와 아닌경우를 나누어두지 않으면 다른 시장을 2번 클릭해야 본래 클릭되어진 시장이 원래대로 돌아오고 새로운 시장이 클릭된듯이 나타남
    // 조건문이라서 클래스바인딩에 어떻게 조건 추가하면 될 듯하긴 하나 좀 어려워보임
    selectMarket(i){
      if(this.focusOn === false){
        this.focusOn = !this.focusOn
        this.num = i
      } else {
        this.num = i
      }
    },
    getMarketInfo(name){
      // 아래 3줄은 레이아웃용 클릭 시 화면 나오고 들어가기
      if(!this.isMarketInfoOpen){
        // this.isMarketInfoOpen ? this.isMarketInfoOpen = false : this.isMarketInfoOpen = true
        this.isMarketInfoOpen = this.isMarketInfoOpen ? false : true
      }
      // console.log(name)
      const selectname = name;
      let selectnameEn = '';
      // console.log(market)
      market.filter((data) => {
        // console.log(data.name)
        if(data.name === selectname ){
          selectnameEn = data.nameEn
        }
      })
      // console.log(selectnameEn)
      axios.get(`https://apis.data.go.kr/3470000/dalseoMarket/viewStoreListIn${selectnameEn}?serviceKey=${this.dalseomykey}`)
      .then((res) => {
          // console.log(res.data.body.totalCount)
          // console.log(res.data.body.items)
          // 시장별 음식점 수 구하기
          this.marketTotalCount = res.data.body.totalCount
          // console.log(this.marketTotalCount)
          // 시장별 음식점 수를 집어넣어 전체 정보 불러오기
           axios.get(`https://apis.data.go.kr/3470000/dalseoMarket/viewStoreListIn${selectnameEn}?serviceKey=${this.dalseomykey}&numOfRows=${this.marketTotalCount}`)
            .then((res) => {
              // console.log(res.data.body.items.item)
              this.marketitemlist = res.data.body.items.item

            })
            .catch((error) => {
              console.log(error);
            })

        })
        .catch((error) => {
          console.log(error);
        })
    },
  },
  computed: {
    storeClassifyList() {
      return this.marketitemlist.filter((item, i) => {
        return (
          this.marketitemlist.findIndex((item2) => {
            return item.storeClassify === item2.storeClassify
          }) === i
        )
      })
    },
    storeClassifyItem() {
      return this.marketitemlist.filter((data) => {
        // console.log(data.storeClassify)
        if (this.storeClassify !== '전체') {
          return data.storeClassify === this.storeClassify
        } else {
          return data.storeClassify
        }
      })
    },
  },
}
</script>
