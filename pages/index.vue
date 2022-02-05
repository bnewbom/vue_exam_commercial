<template>
  <div>
    <h3>사용자 정보 입력</h3>
    <v-form v-model="stepOneVaild">      
      <v-text-field
        v-model="usrName"
        :rules="nameRules"
        label="이름"
        required
      ></v-text-field>

      <v-text-field
        v-model="phone"
        :rules="phoneRules"
        :counter="13"
        label="번호"
        required
      ></v-text-field>

      <v-text-field
        v-model="email"
        :rules="emailRules"
        label="이메일"
        required
      ></v-text-field>
      <v-btn
      disabled
      class="mr-4"
      >
        이전
      </v-btn>
      <v-btn
        :disabled="!stepOneVaild"
        class="success"
        @click="validate"
      >
        다음
      </v-btn>
    </v-form>

    <v-form v-model="stepTwoValid">      
      <input type="text" id="sample4_postcode" placeholder="우편번호">
      <input type="button" @click="sample4_execDaumPostcode" value="우편번호 찾기"><br>
      <input type="text" id="sample4_roadAddress" placeholder="도로명주소">
      <input type="text" id="sample4_jibunAddress" placeholder="지번주소">
      <span id="guide" style="color:#999;display:none"></span>
      <input type="text" id="sample4_detailAddress" placeholder="상세주소">
      <input type="text" id="sample4_extraAddress" placeholder="참고항목">
      <v-btn
      disabled
      class="mr-4"
    >
      이전
    </v-btn>
      <v-btn
      :disabled="!stepTwoValid"
      class="success"
      @click="validate"
    >
      다음
    </v-btn>
    </v-form>

    <v-form v-model="stepThreeValid">      
      <v-text-field
        v-model="cardNum"
        :rules="cardRules"
        label="신용카드"
        required
      ></v-text-field>
      <v-btn
      disabled
      class="mr-4"
    >
      이전
    </v-btn>
      <v-btn
      :disabled="!stepThreeValid"
      class="success"
      @click="validate"
    >
      다음
    </v-btn>
    </v-form>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data(){
    return{
      stepOneVaild: false,
      stepTwoValid: false,
      stepThreeValid: false,
      usrName: '',
      nameRules: [],
      phone:'',
      phoneRules: [],
      email: '',
      emailRules: [],
      cardNum:'',
      cardRules:[]
    }
  },
  methods:{
  }
}
</script>
<style scoped>
.success{
  color: #fff;
  background: #C62828;
}
</style>
