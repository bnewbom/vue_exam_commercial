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
        :counter="19"
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

<script src="//t1.daumcdn.net/mapjsapi/bundle/postcode/prod/postcode.v2.js"></script>
<script>
export default {
  name: 'IndexPage',
  data(){
    return{
      stepOneVaild: false,
      stepTwoValid: false,
      stepThreeValid: false,
      usrName: '',
      nameRules: [
        v => !!v || '필수 입력 필드입니다.',
        v => this.validName(v) || '이름 형식이 맞지 않습니다. (한글 또는 영문 2자 이상)',
      ],
      phone:'',
      phoneRules: [
        v => !!v || '필수 입력 필드입니다.',
        v => this.validPhone(v) || '핸드폰 번호 형식이 맞지 않습니다.',
      ],
      email: '',
      emailRules: [
        v => !!v || '필수 입력 필드입니다.',
        v => this.validEmail(v) || '이메일 형식이 맞지 않습니다.',
      ],
      cardNum:'',
      cardRules:[
        v => !!v || '필수 입력 필드입니다.',
        v => this.validCard(v) || '신용카드 형식이 맞지 않습니다.',
      ]
    }
  },
  methods:{
    validate () {
      console.log(this.$refs.form)
        
    },

    validName(v){
      return /^[가-힣a-zA-Z]{2}$/.test(v)
    },

    validPhone(v){
      return /^01([0|1|6|7|8|9])[- ]?([0-9]{3,4})[- ]?([0-9]{4})$/.test(v)
    },

    validEmail(v){
      return /^[0-9a-zA-Z]([-_.]?[0-9a-zA-Z])*@[0-9a-zA-Z]([-_.]?[0-9a-zA-Z])*.[a-zA-Z]{2,3}$/i.test(v)
    },

    validCard(v){
      if(/^([0-9]{4})[- ]?([0-9]{4})[- ]?([0-9]{4})$/.test(v)){
        this.validCardNum(v)
      }else{
        return false
      }
    },

    validCardNum(v){
      //카드 번호 유효성 체크
      //뒤에서부터 홀수번째 자리의 숫자를 모두 더한 값을 홀수합
      //뒤에서부터 짝수번째 자리의 숫자에 2을 곱하되, 만일 2를 곱한 수가 두자리수 숫자일 경우 두 수를 더 한 값을, 한자 리수라면 그 숫자 그대로를 모두 더한 값이 짝수합
      //홀수합과 짝수합을 더한 값이 10의 배수이면 유효
      v = v.replace(/\s|-/gi,'').split('').reverse()

      let odd = v.filter((el,index)=>{
        return index % 2 == 0
      })
      let oddSum = 0
      odd.forEach(el=>{oddSum += Number(el)})

      let even = v.filter((el,index)=>{
        return index % 2 != 0
      })
      let evenSum = 0
      even.forEach(el=>{
        if(Number(el)*2 >= 10){
          let temp = (Number(el)*2).toString().split('')
          evenSum += Number(temp[0]) 
          evenSum += Number(temp[1]) 
        }else{
          evenSum += Number(el)*2
        }
      })
      return (oddSum + evenSum) % 10 == 0 ? true : false 
    },


    sample4_execDaumPostcode() {
        new daum.Postcode({
            oncomplete: function(data) {
                // 팝업에서 검색결과 항목을 클릭했을때 실행할 코드를 작성하는 부분.

                // 도로명 주소의 노출 규칙에 따라 주소를 표시한다.
                // 내려오는 변수가 값이 없는 경우엔 공백('')값을 가지므로, 이를 참고하여 분기 한다.
                var roadAddr = data.roadAddress; // 도로명 주소 변수
                var extraRoadAddr = ''; // 참고 항목 변수

                // 법정동명이 있을 경우 추가한다. (법정리는 제외)
                // 법정동의 경우 마지막 문자가 "동/로/가"로 끝난다.
                if(data.bname !== '' && /[동|로|가]$/g.test(data.bname)){
                    extraRoadAddr += data.bname;
                }
                // 건물명이 있고, 공동주택일 경우 추가한다.
                if(data.buildingName !== '' && data.apartment === 'Y'){
                   extraRoadAddr += (extraRoadAddr !== '' ? ', ' + data.buildingName : data.buildingName);
                }
                // 표시할 참고항목이 있을 경우, 괄호까지 추가한 최종 문자열을 만든다.
                if(extraRoadAddr !== ''){
                    extraRoadAddr = ' (' + extraRoadAddr + ')';
                }

                // 우편번호와 주소 정보를 해당 필드에 넣는다.
                document.getElementById('sample4_postcode').value = data.zonecode;
                document.getElementById("sample4_roadAddress").value = roadAddr;
                document.getElementById("sample4_jibunAddress").value = data.jibunAddress;
                
                // 참고항목 문자열이 있을 경우 해당 필드에 넣는다.
                if(roadAddr !== ''){
                    document.getElementById("sample4_extraAddress").value = extraRoadAddr;
                } else {
                    document.getElementById("sample4_extraAddress").value = '';
                }

                var guideTextBox = document.getElementById("guide");
                // 사용자가 '선택 안함'을 클릭한 경우, 예상 주소라는 표시를 해준다.
                if(data.autoRoadAddress) {
                    var expRoadAddr = data.autoRoadAddress + extraRoadAddr;
                    guideTextBox.innerHTML = '(예상 도로명 주소 : ' + expRoadAddr + ')';
                    guideTextBox.style.display = 'block';

                } else if(data.autoJibunAddress) {
                    var expJibunAddr = data.autoJibunAddress;
                    guideTextBox.innerHTML = '(예상 지번 주소 : ' + expJibunAddr + ')';
                    guideTextBox.style.display = 'block';
                } else {
                    guideTextBox.innerHTML = '';
                    guideTextBox.style.display = 'none';
                }
            }
        }).open();
    }
  }
}
</script>
<style scoped>
.success{
  color: #fff;
  background: #C62828;
}
</style>
