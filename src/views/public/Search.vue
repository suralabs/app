<template>
<div class="mx-4 sm:mx-[5%] md:mx-[15%] px-1 sm:px-10 mt-4">
  <div class="w-full sm:grid grid-cols-5 gap-x-4">
    <div class="col-span-2">
      <div class="bg-white dark:bg-slate-900 lg:rounded-2xl">
        <div>
          <ul>
            <li>
              <router-link to="/search" class="active exact-active flex cursor-pointer py-3 text-blue-600 hover:text-black dark:text-white dark:text-slate-300 dark:hover:text-white">
                <span class="inline-flex justify-center items-center w-16 h-6 flex-none">
                  <svg viewBox="0 0 24 24" width="18" height="18" class="inline-block">
                    <path fill="currentColor" d="M21,16H3V4H21M21,2H3C1.89,2 1,2.89 1,4V16A2,2 0 0,0 3,18H10V20H8V22H16V20H14V18H21A2,2 0 0,0 23,16V4C23,2.89 22.1,2 21,2Z"></path>
                  </svg>
                </span>
                <span class="grow text-ellipsis line-clamp-1 pr-12 p2">Все</span>
              </router-link>
            </li>
            <li>
              <router-link to="/search" class="active exact-active flex cursor-pointer py-3 text-blue-600 hover:text-black dark:text-white dark:text-slate-300 dark:hover:text-white">
                <span class="inline-flex justify-center items-center w-16 h-6 flex-none">
                  <svg viewBox="0 0 24 24" width="18" height="18" class="inline-block">
                    <path fill="currentColor" d="M21,16H3V4H21M21,2H3C1.89,2 1,2.89 1,4V16A2,2 0 0,0 3,18H10V20H8V22H16V20H14V18H21A2,2 0 0,0 23,16V4C23,2.89 22.1,2 21,2Z"></path>
                  </svg>
                </span>
                <span class="grow text-ellipsis line-clamp-1 pr-12 p2">Люди</span>
              </router-link>
            </li>            
          </ul>
        </div>
      </div>
    </div>       
    <div class="col-span-3">
      <div class="bg-white dark:bg-slate-900 lg:rounded-2xl">
        <div class="p-6"><h5 class="text-2xl">Результаты поиска</h5></div>
        <div class="mb-6 last:mb-0">
          <div class="">
            <div class="relative">
              <input placeholder="query" 
                v-model="query_variable"
                type="text" class="px-3 py-2 max-w-full focus:ring focus:outline-none border-gray-700 rounded w-full dark:placeholder-gray-400 h-12 border bg-white dark:bg-slate-800">
            </div>
          </div>
        </div>
        <div class="grid-cols-2 my-4 gap-1" v-for="(item, index) in results">
          <router-link :to="'/id' + item.id" class="col-span-1 flex items-center gap-x-3 px-4 py-5">
            <img :src="item.photo_50" alt="" class="w-10 sm:w-16 md:w-20 h-10 sm:h-16 md:h-20 rounded-md object-cover cursor-pointer">
            <div class="">
              <div class="text-[14px] sm:text-[17px] font-semibold cursor-pointer">{{ item.first_name }}</div>
              <div class="text-[12px] sm:text-[14px] dark:text-[#b0b3b8]">Super Idol :v</div>
            </div>
            <button class="px-3 sm:px-4 py-1 md:py-2 ml-auto hover:bg-[#3C4D63] bg-[#3C4D63]/50 transition-20 text-white rounded-md text-[14px] sm:text-base">Unfollow</button>
          </router-link>
        </div>       
      </div>
    </div>
 
  </div>
</div>
</template>

<script>
import Button from '@/ui/button/Button.vue'
import Axios from "axios";

export default {
  name: 'Home',
  components: {
    Button,
    Axios
  },
  props: ['user', 'query'],
  data() {
    return {
      results: [],
      query_variable: ''
    }
  },
  watch: {
    // whenever question changes, this function will run
    query_variable(newQuery, oldQuery) {
      this.change_query()
    }
  },  
  async mounted() {
    this.check_query()
    await Axios.post(import.meta.env.VITE_DOMAIN_API + "search", {
      access_token: localStorage.getItem('token'),        
      query: this.query_variable,
    })
    .then(res => {
      if (res.data.status == 1) {
        this.results = res.data.data.results;
      } else {
        // this.user.is_connected = false;
      }
    })    
  },
  methods: { 
    change_query(){    
      Axios.post(import.meta.env.VITE_DOMAIN_API + "search", {
        access_token: localStorage.getItem('token'),        
        query: this.query_variable,
      })
      .then(res => {
        if (res.data.status == 1) {
          this.results = res.data.data.results;
        } else {
          // this.user.is_connected = false;
        }
      }) 
      
      if (this.query_variable == null) {
        this.$router.push('/search/')
      }else{
        this.$router.push('/search/' + this.query_variable)
      }
    },
    check_query(){
      if (this.query !== null) {
        this.query_variable = this.query
      }
    }
  },
}
</script>