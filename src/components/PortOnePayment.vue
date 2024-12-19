<template>
  <div>
    <label for="merchantUid">가맹점 주문번호</label>
    <input id="merchantUid" v-model="merchantUid" type="text" placeholder="주문번호를 입력하세요" required />

    <label for="productName">결제대상 제품명</label>
    <input id="productName" v-model="productName" type="text" placeholder="결제할 제품명 입력하세요" required />

    <label for="amount">결제금액</label>
    <input id="amount" v-model="amount" type="text" @input="validateAmount" placeholder="결제금액을 입력하세요" required />

    <button @click="callIamport">결제하기</button>
  </div>
</template>

<script setup>
import {ref} from "vue";
import { onMounted } from "vue";

let IMP = null;
const merchantUid = ref(""); // 가맹점 주문번호
const productName = ref(""); // 결제 대상 제품명
const amount = ref(0);       // 결제 금액

const validateAmount = (event) => {
  const input = event.target.value;
  amount.value = input.replace(/[^0-9]/g, "");
};

onMounted(() => {
  if (window.IMP) {
    IMP = window.IMP;
    IMP.init("imp30386132"); // 고객사 식별코드
    console.log("SDK Success");
  } else {
    console.error("SDK Fail");
  }
});

const callIamport = () => {
  console.log("가맹점 주문번호:", merchantUid.value);
  console.log("결제 대상 제품명:", productName.value);
  console.log("결제 금액:", amount.value);

  if (!IMP) {
    alert("iamport 스크립트 호출 오류");
    return;
  }

  // S : input 검증
  if (!merchantUid.value.trim()) {
    alert("가맹점 주문번호를 입력해주세요.");
    return;
  }

  if (!productName.value.trim()) {
    alert("결제 대상 제품명을 입력해주세요.");
    return;
  }

  const amountValue = parseInt(amount.value, 10);
  if (isNaN(amountValue) || amountValue <= 0) {
    alert("결제 금액을 입력해 주세요.");
    return;
  }
  // E : input 검증

  IMP.request_pay({
    channelKey: "channel-key-9b00c5a7-9877-481c-8ce5-91252cd1df08",
    pay_method: "card",
    merchant_uid: merchantUid.value, // 주문 고유 번호 ex.order_no_0001
    name: productName.value, // 결제 대상 제품명 ex.노르웨이 회전 의자
    amount: amount.value,
    buyer_email: "anna_y00@icloud.com",
    buyer_name: "유안나",
    buyer_tel: "010-4433-3101",
    buyer_addr: "서울특별시 강남구 삼성동",
    buyer_postcode: "123-456",
    language: "ko", // 결제창 언어 선택 파라미터  ko: 한국어, en: 영문
  }, (response) => {
    if (response.success) {
      alert("결제가 성공적으로 처리되었습니다");
      console.log(response);
      //input 초기화
      merchantUid.value = "";
      productName.value = "";
      amount.value = "";
    } else {
      alert(`결제에 실패하였습니다 (${response.error_msg})`);
      console.error(response);
    }
  });
};
</script>

<style scoped>
div {
  max-width: 350px;
  margin: 20px auto;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  font-size: 14px;
  color: #FC6B2D;
}

input {
  width: 100%;
  padding: 10px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-bottom: 15px;
  box-sizing: border-box;
  transition: border-color 0.3s;
  text-align: center;
}

input:focus {
  border-color: #FC5005;
}

button {
  display: block;
  width: 100%;
  padding: 10px 0;
  font-size: 16px;
  cursor: pointer;
  background-color: #FC6B2D;
  color: white;
  border: none;
  border-radius: 4px;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #EF4A03;
}

</style>