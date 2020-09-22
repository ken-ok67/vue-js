<template>
  <main class="content container">
    <div class="content__top">
      <ul class="breadcrumbs">
        <li class="breadcrumbs__item">
          <a class="breadcrumbs__link" href="index.html">
            Каталог
          </a>
        </li>
        <li class="breadcrumbs__item">
          <a class="breadcrumbs__link" href="cart.html">
            Корзина
          </a>
        </li>
        <li class="breadcrumbs__item">
          <a class="breadcrumbs__link">
            Оформление заказа
          </a>
        </li>
      </ul>

      <h1 class="content__title">
        Корзина
      </h1>
      <span class="content__info">
        3 товара
      </span>
    </div>

    <section class="cart">
      <form class="cart__form form" action="#" method="POST" @submit.prevent="order">
        <div class="cart__field">
          <div class="cart__data">
            <BaseFormText title='ФИО' :error="formError.name" type="text"
                          placeholder="Введите ваше полное имя" v-model="formData.name"/>

            <BaseFormText title='Адрес доставки' :error="formError.address" type="text"
                          placeholder="Введите ваш адрес" v-model="formData.address"/>

            <BaseFormText title='Телефон' :error="formError.phone" type="tel"
                          placeholder="Введите ваш телефон" v-model="formData.phone"/>

            <BaseFormText title='Email' :error="formError.email" type="email"
                          placeholder="Введи ваш Email" v-model="formData.email"/>

            <BaseFormTextarea title="Комментарий к заказу" placeholder="Ваши пожелания"
                              :error="formError.comment" v-model="formData.comment" />
          </div>

          <div class="cart__options">
            <h3 class="cart__title">Доставка</h3>
            <ul class="cart__options options">
              <li class="options__item">
                <label class="options__label">
                  <input class="options__radio sr-only"
                         type="radio" name="delivery" value="0" v-model="delivery">
                  <span class="options__value">
                    Самовывоз <b>бесплатно</b>
                  </span>
                </label>
              </li>
              <li class="options__item">
                <label class="options__label">
                  <input class="options__radio sr-only" value="500" type="radio" v-model="delivery"
                         name="delivery">
                  <span class="options__value">
                    Курьером <b>500 ₽</b>
                  </span>
                </label>
              </li>
            </ul>

            <h3 class="cart__title">Оплата</h3>
            <ul class="cart__options options">
              <li class="options__item">
                <label class="options__label">
                  <input class="options__radio sr-only" type="radio" name="pay"
                         value="Картой при получении" v-model="pay">
                  <span class="options__value">
                    Картой при получении
                  </span>
                </label>
              </li>
              <li class="options__item">
                <label class="options__label">
                  <input class="options__radio sr-only" type="radio" name="pay"
                         value="Наличными при получении" v-model="pay">
                  <span class="options__value">
                    Наличными при получении
                  </span>
                </label>
              </li>
            </ul>
          </div>
        </div>

        <div class="cart__block">
          <ul class="cart__orders">
            <li class="cart__order" v-for="item in products" :key="item.productId">
              <h3>{{ item.product.title }}</h3>
              <b>{{ item.amount * item.product.price | numberFormat }} ₽</b>
              <span>Артикул: {{ item.productId }}</span>
            </li>
          </ul>

          <div class="cart__total">
            <p>Доставка: <b>{{ delivery }} ₽</b></p>
            <p>Итого: <b>{{ amount }}
            </b> товара на сумму <b>{{ parseInt(delivery, 10) + totalPrice | numberFormat }} ₽</b>
            </p>
          </div>

          <button class="cart__button button button--primery" type="submit">
            Оформить заказ
          </button>
        </div>
        <div class="cart__error form__error-block" v-if="formErrorMessage">
          <h4>Заявка не отправлена!</h4>
          <p>
            {{ formErrorMessage }}
          </p>
        </div>
      </form>
    </section>
  </main>
</template>

<script>
import BaseFormText from '@/components/BaseFormText.vue';
import BaseFormTextarea from '@/components/BaseFormTextarea.vue';
import axios from 'axios';
import { API_BASE_URL } from '@/config';
import numberFormat from '@/helpers/numberFormat';
import { mapGetters } from 'vuex';

export default {
  name: 'OrderPage',
  components: { BaseFormText, BaseFormTextarea },
  data() {
    return {
      formData: {},
      formError: {},
      formErrorMessage: '',
      delivery: '0',
      pay: '',
    };
  },
  filters: { numberFormat },
  computed: {
    ...mapGetters({ products: 'cartDetailProducts', totalPrice: 'cartTotalPrice' }),
    amount() {
      return this.products.reduce((value, item) => value + item.amount, 0);
    },
  },
  methods: {
    order() {
      this.formError = {};
      this.formErrorMessage = '';
      axios.post(`${API_BASE_URL}/api/orders`, {
        ...this.formData,
      }, {
        params: {
          userAccessKey: this.$store.state.userAccessKey,
        },
      })
        .then((response) => {
          this.$store.commit('resetCart');
          this.$store.commit('updateOrderInfo', response.data);
          this.$store.commit('updateDelivery', this.delivery);
          this.$store.commit('updatePay', this.pay);
          this.$router.push({ name: 'orderInfo', params: { id: response.data.id } });
        })
        .catch((error) => {
          this.formError = error.response.data.error.request || {};
          this.formErrorMessage = error.response.data.error.message;
        });
    },
  },
};
</script>
