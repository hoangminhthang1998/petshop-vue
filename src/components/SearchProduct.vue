<template>
  <div class="searchproduct">
    <form v-on:submit.prevent="onShowFull" style="position: relative; z-index: 100">
      <input
        class="header__top__mid__search__item"
        name="text_search"
        type="search"
        placeholder="Tìm kiếm sản phẩm..."
        v-model="searchQuery"
        style="padding-left: 8px; padding-right: 60px;"
      />
      <a-icon type="search" class="icon-x"  @click="onShowFull" style="font-size: 20px; top: -10px"/>
      <a-icon v-if="searchQuery" type="close" class="icon-x" style="right: 35px; top: 2px" @click="searchQuery = ''"/>
    </form>    
    <!-- <i v-if="searchQuery" class="fas fa-times-circle icon-x" alt="x" @click="searchQuery = ''"></i> -->

    <div
      v-if="productAll.length > resultQuery.length  && resultQuery.length>0"
      class="drop_search"
      id="container"
    >
        <div
          class="version-type"
          v-for="(item, key) in isShowFull ? resultQuery : (productAll.length > resultQuery.length  && resultQuery.slice(0,5))"
          :key="key"
          @click="onClickProduct(item.type, item.detail, item.id)"
        >
          <!-- @click="onClickProduct(index, index1, index2, listProduct[index].i)" -->
          <img x="0" y="0" height="100%" preserveAspectRatio="xMidYMid slice" width="100%" class="col-2" :src="item.image" style="maxwidth: 30%" />
          <div class="col-10">
            <p>{{ item.title }}</p>
          </div>
      </div>
      <!-- onShowFullProductSearch -->
      <!-- isShowFull = true -->
      <div v-if="resultQuery.length>5 && !isShowFull" @click="onShowFull" style="padding: 5px 10px">Xem thêm...</div>
    </div>
    <div v-if="searchQuery" @click="searchQuery = ''" class="overlay"></div>
  </div>
</template>

<script>
import {RepositoryFactory} from '../api/RepositoryFactory';
const PostsRepository = RepositoryFactory.communicationAPI('posts')
export default {
    data() {
        return {
            searchQuery: '',
            productAll:[],
            resources:[
                {title:"ABE Attendance",uri:"aaaa.com",category:"a",icon:null},
                {title:"Accounting Services",uri:"aaaa.com",category:"a",icon:null},
                {title:"Administration",uri:"aaaa.com",category:"a",icon:null},
                {title:"Advanced Student Lookup",uri:"bbbb.com",category:"b",icon:null},
                {title:"Art & Sciences",uri:"bbbb.com",category:"b",icon:null},
                {title:"Auxiliares Services",uri:"bbbb.com",category:"b",icon:null},
                {title:"Basic Skills",uri:"cccc.com",category:"c",icon:null},
                {title:"Board of Trustees",uri:"dddd.com",category:"d",icon:null}
            ],
            isShowFull: false,
            thumbnails: []
        }
    },
    created(){
        this.fetch()
    },
    computed:{ 
        resultQuery(){
          let productSearch = this.productAll
          if(this.searchQuery){
            return productSearch.filter((item)=>{
              return this.searchQuery.toLowerCase().split(' ').every(v => item.title.toLowerCase().includes(v))
            })
          }
          else{
            return productSearch;
          }
        }
    },
    watch: {
      searchQuery(){
        this.isShowFull = false
      }
    },
    methods: {
      onShowFull(){
        // const a = [1,2,3,4]
        // console.log(this.resultQuery);
                        // this.$bus.emit('increaseCounter', this.users[i].fullname),
        this.$bus.emit('resultQuery', this.resultQuery),
        this.$router.push(`/product/text_search=${this.searchQuery}`)
        this.searchQuery = ''
      },
      // onShowFullProductSearch(){

      // }
      async fetch(){
        const {data} = await PostsRepository.getProductDetail();
        this.productAll = data
        this.productAllBackup = data

        console.log(data);
        console.log(this.productAll);
      },
      onClickProduct(type, detail, id){
        this.$router.push(`/purchase/${type}/${detail}/${id}`)
        this.searchQuery = ''
      }
    },
};
</script>

<style scoped lang="scss">
.searchproduct{
  height: 48px;
  position: relative;
  >input{
    padding: 10px 30px 10px 10px ;
    position: absolute;
    z-index: 101;
    &:focus-visible{
      outline: #bdbdbd;
    }
  }
  .icon-x{
    position: absolute;
    z-index: 102;
    right: 12px;
    top: 0;
    transform: translate(0%, 100%);
  }
  .drop_search{
    height: auto;
    max-height: 294px;
    padding-top: 3px;
    overflow-y: scroll;
    border: 1px solid #bdbdbd;
    position: absolute;
    top: 36px;
    z-index: 100;
    background-color: white;
    width: 100%;
    border-bottom-left-radius: 4px;
    border-bottom-right-radius: 4px;
    img{
      padding: 5px;
    }
  }
  .version-type{
    cursor: pointer;
    display: flex;
    padding: 5px 10px;
    &:hover{
      background-color: rgb(235, 235, 235);
    }
  }
  .overlay{
    position: fixed;
    top: 0;
    left: 0;
    z-index: 99;
    width: 100%;
    height: 100%;
    opacity: 0.4;
    background-color: black;
  }
  ::-webkit-scrollbar {
      width: 6px;
  }
  ::-webkit-scrollbar-track {
      background: #f1f1f1; 
  }
  ::-webkit-scrollbar-thumb {
      background: #bdbdbd;
      border-radius: 2px;
  }
  ::-webkit-scrollbar-thumb:hover {
      background: #dddddd; 
  }
}
</style>