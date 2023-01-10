<template>
  <main class="app__container">
    <fieldset class="flex-column">
      <legend>Данные для парсинга</legend>
      <div class="flex-column center">
        <div class="flex-row">
          <label for="name">Введите наименование</label>
          <input v-model="productName" id="name" type="text" />
        </div>
        <div class="flex-row">
          <label for="name">Введите бренд</label>
          <input v-model="productBrand" id="brand" type="text" />
        </div>
      </div>
      <button @click="parser">Поиск</button>
    </fieldset>

    <ul>
      <li
        v-for="(brand, i) in brandList"
        :key="i"
        :class="{
          bigGreen: brand.toUpperCase() === productBrand.toUpperCase(),
        }"
      >
        {{ brand }}
      </li>
    </ul>
  </main>
</template>

<script setup>
import { ref, watch } from "vue";
import axios from "axios";

const productName = ref("");
const productBrand = ref("");
const brandList = ref(new Set());

function parser() {
  brandList.value.clear();
  axios
    .get(
      `https://search.wb.ru/exactmatch/ru/common/v4/search?appType=1&dest=-1029256,-102269,-2162196,-1257786&locale=ru&query=${productName.value}&resultset=catalog`
    )
    .then((result) => {
      if (result.data.data) {
        result.data.data.products.forEach((product) => {
          brandList.value.add(product.brand);
        });
      }
    })
    .catch((error) => console.log(error));
}

watch(productName, () => {
  parser();
});
</script>

<style lang="scss">
.app {
  &__container {
    max-width: 767px;
    margin: auto;
  }
}
.bigGreen {
  font-weight: 700;
  text-decoration: underline;
  color: green;
  font-size: 24px;
}
.flex-row {
  display: flex;
  flex-direction: row;
  gap: 10px;
}
.flex-column {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.center {
  align-items: center;
  justify-content: center;
  text-align: center;
}
</style>
