<template>
    <div class="star-ratings">
        <div 
        class="star-ratings-fill space-x-2 text-lg"
        :style="{ width: ratingToPercent + '%' }"
        >
            <span>★</span><span>★</span><span>★</span><span>★</span><span>★</span>
        </div>
        <div class="star-ratings-base space-x-2 text-lg">
            <span>★</span><span>★</span><span>★</span><span>★</span><span>★</span>
        </div>
    </div>
</template>

<script>
export default {
    // data(){
    //     return{
    //         score: 2.7
    //     }
    // },
    props:['score'],
    computed:{
        ratingToPercent() {
            const score = + this.score * 20;
            return score;
        }
    }
}
</script>

<style scoped>
.star-ratings {
  color: #aaa9a9; 
  position: relative;
  unicode-bidi: bidi-override;
  width: max-content;
  -webkit-text-fill-color: transparent; /* Will override color (regardless of order) */
  -webkit-text-stroke-width: 1px;
  -webkit-text-stroke-color: #2b2a29;
}
 
.star-ratings-fill {
  color: #C62828;
  padding: 0;
  position: absolute;
  z-index: 1;
  display: flex;
  top: 0;
  left: 0;
  overflow: hidden;
  -webkit-text-fill-color: #ffe601;
}
 
.star-ratings-base {
  z-index: 0;
  padding: 0;
}
</style>