<template>
    <div class="container">
        <h3>상품 목록</h3>
        <v-list>
            <template v-for="(item, index) in items">
                <v-list-item
                :key="item.idBrand"
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
            items:''
        }
    },
    mounted(){
        this.getProducts()
    },
    methods:{
        async getProducts() {
            try{
                //CORS 해결을 위해 'https://cors-anywhere.herokuapp.com/' 테스트용 프록시 서버 사용하여 우회함.
                const { data } = await this.$axios.get('https://cors-anywhere.herokuapp.com/https://s3.ap-northeast-2.amazonaws.com/public.glowday.com/test/app/products.json')
                if (data.products.length > 0) {
                    this.items = data.products
                }
            }
            catch(e){
                console.log(e)
                return false
            }
        },
    }
}
</script>

<style scoped>
.container{
  max-width: 50%;
  margin: 0 auto;
  margin-top: 100px;
}
.brand{
    color: #505050;
    font-size: 12px;
}
.title{
    font-weight: bold;
}
</style>

