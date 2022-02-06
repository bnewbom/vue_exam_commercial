<template>
    <div class="container">
        <div class="topMenu">
            <h3>상품 목록</h3>
            <div>
                <span>평점순</span> 
                <button @click="toggleAsc">
                    <span v-if="isAsc">높은순</span>
                    <span v-else>낮은순</span>
                </button>
            </div>
            <div>
                <span>필터</span> 
                <button :class="{ active: this.showFilter == 'All'}" @click="setFillter('All')">모두</button>
                <button :class="{ active: this.showFilter == 'Positive'}" @click="setFillter('Positive')">긍정적</button>
                <button :class="{ active: this.showFilter == 'Negative'}" @click="setFillter('Negative')">부정적</button>
            </div>
            <div>
                <span>제품명</span> 
                <input type="text" v-model="searchName" placeholder="제품명 검색" @input="searchByName">
            </div>
        </div>
        <v-list class="productList">
            <template v-for="(item, index) in items">
                <v-list-item 
                class="listItem"
                :key="item.idProduct"
                >
                    <v-list-item-avatar style="width:120px; height:120px; margin-right:10px">
                        <v-img :src="item.imageUrl"></v-img>
                    </v-list-item-avatar>

                    <v-list-item-content>
                        <v-list-item-title class="brand">{{item.brand.brandTitle}}</v-list-item-title>
                        <v-list-item-title class="title">{{item.productTitle}}</v-list-item-title>
                        <v-list-item-subtitle>
                            <ratingStar class="star" :score="item.ratingAvg"></ratingStar>
                            <span class="review">{{item.ratingAvg}} (리뷰 {{item.reviewCount}})</span>
                        </v-list-item-subtitle>
                        <v-list-item-subtitle><span class="price">{{item.volume}}/{{item.price}}원</span></v-list-item-subtitle>
                    </v-list-item-content>
                </v-list-item>
            </template>
        </v-list>    
    </div>
</template>


<script>
import ratingStar from '@/components/ratingStar.vue'
export default{
    data(){
        return{
            products:'',
            items:'',
            sortedItem:'',
            isAsc: true,
            showFilter: 'All',
            searchName:'',
            count: 1,
            bottom: false,
        }
    },
    components:{
        ratingStar
    },
    created() {
        window.addEventListener('scroll', () => {
        this.bottom = this.bottomVisible()
        })
    },
    mounted(){
        this.getProducts()
    },
    watch: {
    bottom(bottom) {
      if (bottom) {
        this.count ++
        this.addItem()
      }
    }
  },
    methods:{
        async getProducts() {
            try{
                //CORS 해결을 위해 'https://cors-anywhere.herokuapp.com/' 테스트용 프록시 서버 사용하여 우회함.
                const { data } = await this.$axios.get('https://cors-anywhere.herokuapp.com/https://s3.ap-northeast-2.amazonaws.com/public.glowday.com/test/app/products.json')
                if (data.products.length > 0) {
                    this.products = data.products
                }
                this.setItem()
            }
            catch(e){
                console.log(e)
                return false
            }
        },

        bottomVisible() {
            const scrollY = window.scrollY
            const visible = document.documentElement.clientHeight
            const pageHeight = document.documentElement.scrollHeight
            const bottomOfPage = visible + scrollY >= pageHeight
            return bottomOfPage || pageHeight < visible
        },

        addItem(){
            console.log('addItem',this.sortedItem)
            let size = this.count * 10
            if(size <= this.sortedItem.length){
                this.items = this.sortedItem.slice(0, size) 
            }else{
                this.items = this.sortedItem
            }
        },

        setItem(){   
            this.count = 1
            this.sortItem()
            this.filterItem()    
            this.items = this.sortedItem.slice(0, 10) 
        },

        toggleAsc(){
            this.isAsc = !this.isAsc
            this.setItem()
        },

        sortItem(){ 
            if(this.isAsc){
                this.sortedItem = this.products.sort((a, b) => {
                    return b.ratingAvg - a.ratingAvg
                })
            } else {
                this.sortedItem = this.products.sort((a, b) =>{
                    return a.ratingAvg - b.ratingAvg
                })
            }
        },

        setFillter(str){
            this.showFilter = str
            this.setItem() 
        },

        filterItem(){
            if(this.showFilter == 'Positive'){
                this.sortedItem = this.sortedItem.filter(el=>{
                    return el.ratingAvg >= 3
                })
            }else if(this.showFilter == 'Negative'){
                this.sortedItem = this.sortedItem.filter(el=>{
                    return el.ratingAvg < 3
                })
            }
        },
        
        searchByName(e){
            this.searchName = e.target.value
            this.searchName = this.searchName.trim()
            if(this.searchName != '' && this.searchName.length >= 2){
               clearTimeout(this.debounce) 
               this.debounce = setTimeout(() => {
                    const temp = this.sortedItem.filter(el => el.productTitle.includes(this.searchName)) 
                    this.sortedItem = temp
                    console.log(this.sortedItem)
                    let count = 0
                    if(this.sortedItem.length >= 10){
                        count = 10
                    }else{
                        count = this.sortedItem.length
                    }
                    this.items = this.sortedItem.slice(0, count)
                }, 1000)
            }else{ 
                clearTimeout(this.debounce) 
                this.debounce = setTimeout(() => { this.setItem() }, 1000);  
            }
        }
    }
}
</script>
<style scoped>
.container{
  max-width: 50%;
  margin: 0 auto;
  margin-top: 100px;
}
.topMenu{
    position: fixed;
    top: 0;
    width:930px;
    padding-top: 100px;
    z-index: 10;
    background: #fff;
    border-bottom: 1px solid #eee;
}
.topMenu h3{
    padding-bottom: 10px;
}
.topMenu div{
    padding-bottom: 10px;
    font-size:14px
}
.topMenu span{
    font-weight:bold;
    display: inline-block;
    width:50px;
    margin-right:5px
}
.topMenu button{
    background:#eee;
    font-size:12px;
    padding: 5px;
    border-radius: 10px;
}
.topMenu .active{
    color: #fff;
    background: #C62828;
}
.topMenu input{
    padding: 5px;
    border: 1px solid #eee;
    border-radius: 5px;
}
.topMenu input::placeholder{
    font-size:12px
}
.productList{
    position:relative;
    top:130px
}
.listItem{
    border-bottom:1px solid #eee
}
.brand{
    color: #505050;
    font-size: 12px;
}
.title{
    font-weight: bold;
}
.star{
    float: left;
}
.review{
    float: left;
    margin-left: 5px;
    margin-top: 2px;
    font-size: 12px;
    font-weight: 600;
    color: #000;
}
.price{
    font-size: 12px;
    color: #8b8b8b;
}
</style>

