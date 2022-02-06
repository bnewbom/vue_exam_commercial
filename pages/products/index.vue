<template>
    <div class="container">
        <h3>상품 목록</h3>
        <div class="borderBottom">
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
        <v-list>
            <template v-for="(item, index) in items">
                <v-list-item
                :key="item.idProduct"
                class="borderBottom"
                >
                    <v-list-item-avatar style="width:120px; height:120px; margin-right:10px">
                        <v-img :src="item.imageUrl"></v-img>
                    </v-list-item-avatar>

                    <v-list-item-content>
                        <v-list-item-title class="brand">{{item.brand.brandTitle}}</v-list-item-title>
                        <v-list-item-title class="title">{{item.productTitle}}</v-list-item-title>
                        <v-list-item-subtitle>{{item.ratingAvg}} (리뷰 {{item.reviewCount}})</v-list-item-subtitle>
                        <v-list-item-subtitle>{{item.volume}}/{{item.price}}원</v-list-item-subtitle>
                    </v-list-item-content>
                </v-list-item>
            </template>
        </v-list>
    </div>
</template>


<script>
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
.borderBottom{
    border-bottom: 1px solid #eee;
}
.brand{
    color: #505050;
    font-size: 12px;
}
.title{
    font-weight: bold;
}
.active{
    color: #fff;
    background: #C62828;
}
</style>

