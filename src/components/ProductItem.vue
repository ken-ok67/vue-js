<template>
  <li class="catalog__item">
    <router-link class="catalog__pic" :to="{name: 'product', params: {id: product.id}}">
      <img :src="product.img" :alt="product.img">
    </router-link>

    <h3 class="catalog__title">
      <a href="#">
        {{ product.title }}
      </a>
    </h3>

    <span class="catalog__price">
              {{ product.price | numberFormat }}
            </span>

    <ul class="colors colors--black">
      <li class="colors__item" v-for="color in product.colors" :key="color.id">
        <label class="colors__label">
          <input class="colors__radio sr-only" type="radio">
          <span class="colors__value" :style="{ backgroundColor: color.code }">
                  </span>
        </label>
      </li>
    </ul>
  </li>
</template>

<script>
import goToPage from '@/helpers/goToPage';
import numberFormat from '@/helpers/numberFormat';

export default {
  name: 'ProductItem',
  props: ['product'],
  data() {
    return {
      color: '#73B6EA',
    };
  },
  methods: {
    goToPage,
  },
  filters: {
    numberFormat,
  },
};
</script>
