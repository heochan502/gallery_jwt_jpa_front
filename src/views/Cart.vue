<script setup>
import { getItems, removeItem, clearCart } from '@/Services/cartService';
import { onMounted, reactive, computed } from 'vue';

const state = reactive({
  items: [],
});

// const total = () => {
//   let test =
//     state.items.price - (state.items.price * state.items.discountPer) / 100;
//   return test.valueOf;
// };

// const total = () => {
//   return state.items.reduce((sum, items) => sum + items.value, 0);
// };

const total = computed(() => {
  return state.items.reduce((sum, item) => {
    const discounted = item.price - (item.price * item.discountPer) / 100;
    return sum + discounted;
  }, 0);
});

// let total = 0;

//장바구니 상품 조회
const load = async () => {
  const res = await getItems();

  if (res.status === 200) {
    state.items = res.data;
    // total();
  }

  //   console.log('상태 : ', state.items);
};

const clear = async () => {
  const res = await clearCart();
  if (res.status === 200) {
    alert('모든 장바구니의 상품을 삭제 했습니다.');
    state.items = [];
  }
  await load();
};

//장바구니 상품 삭제
const remove = async (cartId) => {
  const res = await removeItem(cartId);
  console.log('res: ', res);
  if (res.status === 200) {
    alert('선택하신 장바구니의 상품을 삭제 했습니다.');
  }
  await load();
  //다시 리로딩
  //or
  //삭제한 객체만 state.items에서 삭제
};

onMounted(() => {
  load();
});
</script>

<template>
  <div class="cart">
    <div class="container">
      <template v-if="state.items.length">
        <ul class="items">
          <li v-for="i in state.items">
            
            <img
              :alt="`상품 사진 (${i.name})`"
              :src="`/pic/item/${i.imgPath}`"
            />
            <b class="name">{{ i.name }}</b>
            <span class="price">
              {{ (i.price - (i.price * i.discountPer) / 100).toLocaleString() }}
              원
            </span>
            <span class="remove float-end" @click="remove(i.id)" title="삭제"
              >&times;</span
            >
          </li>
          <div class="total">주문 총 금액 : {{ total.toLocaleString() }}</div>
        </ul>
        <div class="act d-flex justify-content-between">
          <button @click="clear()" class="btn btn-danger mb-3">
            장바구니 비우기
          </button>
          <router-link to="/order" class="btn btn-primary"
            >주문하기</router-link
          >
        </div>
      </template>
      <div class="text-center py-5" v-else>장바구니가 비어있습니다.</div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.cart {
  .items {
    list-style: none;
    margin: 0;
    padding: 0;
  }
  li {
    border: 1px solid #eee;
    margin-top: 25px;
    margin-bottom: 25px;
  }
  img {
    width: 150px;
    height: 150px;
  }
  .name {
    margin-left: 25px;
  }
  .price {
    margin-left: 25px;
  }
  .remove {
    cursor: pointer;
    font-size: 30px;
    padding: 5px 15px;
  }
}
.act .btn {
  width: 300px;
  display: block;
  margin: 0 auto;
  padding: 30px 50px;
  font-size: 20px;
}
</style>
