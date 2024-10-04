<template>
  <div class="product-list">
    <h1>Фигурки-аниме (оригинал, не Китай)</h1>
    <div class="products-container">
      <div 
        v-for="product in products" 
        :key="product.id" 
        class="product mb-5" 
      >
        <div :class="['image-container', product.borderClass]">  
          <img :src="product.imageUrl" alt="Изображение товара" class="product-image" />
        </div>
        <div class="product-info">
          <h2 class="product-title">{{ product.name }}</h2>
          <p class="product-price">Цена: {{ product.price }} ₽</p>
          <button @click="addToCart(product)" class="btn btn-cart">
            <span class="cart-icon">🛒</span>
          </button>
        </div>
      </div>
    </div>

    <h2>Корзинка</h2>
    <div v-if="cart.length > 0">
      <h3>Добавляй больше:</h3>
      <ul class="list-group">
        <li v-for="item in cart" :key="item.id" class="list-group-item">
          {{ item.name }} - {{ item.price }} ₽
          <div class="quantity-controls">
            <button @click="decreaseQuantity(item)" class="btn btn-quantity">-</button>
            <span>{{ item.quantity }}</span>
            <button @click="increaseQuantity(item)" class="btn btn-quantity">+</button>
            <button @click="removeFromCart(item)" class="btn btn-remove">🗑️</button>
          </div>
        </li>
      </ul>
      <h4>Общая сумма: {{ totalPrice }} ₽</h4>
      <button @click="checkout" class="btn btn-primary mt-3">Оформить заказ</button>
    </div>
    <div v-else>
      <p>Почему корзина пуста? У нас оригинал</p>
    </div>

    <OrderForm v-if="showOrderForm" :cart="cart" @orderCompleted="handleOrderCompletion" />

    <div v-if="orderCompleted" class="order-message">
      <h3>Поздравляю, ваш заказ успешно оформлен! 🎉</h3>
    </div>
  </div>
</template>

<script>
import OrderForm from './OrderForm.vue';

export default {
  components: {
    OrderForm,
  },
  data() {
    return {
      products: [
        { id: 1, name: 'Коллекционная фигурка Funko Boruto with marks (46057)', price: 9999, imageUrl: require('@/assets/boruto.png'), borderClass: 'yellow-border' },
        { id: 2, name: 'Коллекционная фигурка Funko Movies Avatar Neytiri (65642)', price: 9999, imageUrl: require('@/assets/avatar.png'), borderClass: 'blue-border' },
        { id: 3, name: 'Коллекционная фигурка Funko Demon Slayer Tanjiro with Noodles (57346)', price: 9999, imageUrl: require('@/assets/tanjiro.png'), borderClass: 'red-border' },
      ],
      cart: [],
      showOrderForm: false,
      orderCompleted: false,
    };
  },
  computed: {
    totalPrice() {
      return this.cart.reduce((total, item) => total + item.price * item.quantity, 0);
    },
  },
  methods: {
    addToCart(product) {
      const existingProduct = this.cart.find(item => item.id === product.id);
      if (existingProduct) {
        existingProduct.quantity++;
      } else {
        this.cart.push({ ...product, quantity: 1 });
      }
      console.log('Товар добавлен в корзину:', product);
    },
    increaseQuantity(item) {
      item.quantity++;
    },
    decreaseQuantity(item) {
      if (item.quantity > 1) {
        item.quantity--;
      } else {
        this.removeFromCart(item);
      }
    },
    removeFromCart(item) {
      this.cart = this.cart.filter(cartItem => cartItem.id !== item.id);
      console.log('Товар удалён из корзины:', item);
    },
    checkout() {
      this.showOrderForm = true;
      this.orderCompleted = false; // сбрасываем флаг, чтобы отобразить форму заказа
    },
    handleOrderCompletion() {
      this.showOrderForm = false;
      this.orderCompleted = true;
      this.cart = []; // очищаем корзину после оформления заказа
      console.log('Заказ оформлен:', this.cart);
    },
  },
};
</script>

<style scoped>
.products-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around; 
}

.product {
  width: 300px; 
  text-align: center; 
}

.image-container {
  border-width: 2px; /* Толщина рамки */
  border-style: solid; /* Стиль рамки */
  border-radius: 50%; /* Круглая рамка */
  width: 150px; /* Ширина контейнера изображения */
  height: 150px; /* Высота контейнера изображения */
  display: flex; /* Flexbox для центрирования изображения */
  justify-content: center; /* Центрирование по горизонтали */
  align-items: center; /* Центрирование по вертикали */
  overflow: hidden; /* Обрезка изображения по рамке */
  margin: 0 auto; /* Центрирование контейнера */
}

.product-image {
  width: 100%; /* Изображение на всю ширину контейнера */
  height: auto; /* Авто-высота для поддержания пропорций */
}

.product-info {
  margin-top: 10px; /* Отступ сверху для текстовой информации */
}

.btn-cart {
  background-color: #e5ff00; /* Цвет кнопки */
  color: black; /* Цвет текста */
  border: none; /* Убираем обводку */
  border-radius: 5px; /* Скругленные углы */
  padding: 10px 15px; /* Отступы для кнопки */
  cursor: pointer; /* Курсор при наведении */
}

.btn-cart:hover {
  background-color: #c2cc00; /* Цвет кнопки при наведении */
}

.btn-primary {
  background-color: #e5ff00; /* Цвет кнопки оформления заказа */
  color: black; /* Цвет текста кнопки */
  border: none; /* Убираем обводку */
  border-radius: 5px; /* Скругленные углы */
  padding: 10px 15px; /* Отступы для кнопки */
  cursor: pointer; /* Курсор при наведении */
}

.btn-primary:hover {
  background-color: #c2cc00; /* Цвет кнопки при наведении */
}

.list-group {
  list-style-type: none; /* Убираем маркеры списка */
  padding: 0; /* Убираем отступы */
}

.list-group-item {
  padding: 10px; /* Отступы для элементов списка */
  border-bottom: 1px solid #ccc; /* Разделение элементов списка */
}

.quantity-controls {
  display: inline-flex; /* Flexbox для управления количеством */
  align-items: center; /* Центрирование по вертикали */
}

.btn-quantity, .btn-remove {
  background-color: #f8f9fa; /* Цвет кнопок управления количеством */
  border: 1px solid #ccc; /* Обводка */
  margin: 0 5px; /* Отступы */
  padding: 5px 10px; /* Отступы для кнопок */
  cursor: pointer; /* Курсор при наведении */
}

.btn-remove {
  background-color: #dc3545; /* Цвет кнопки удаления */
  color: white; /* Цвет текста */
}

.btn-remove:hover {
  background-color: #c82333; /* Цвет кнопки удаления при наведении */
}

.order-message {
  margin-top: 20px; /* Отступ сверху для сообщения об успехе */
  color: green; /* Цвет текста сообщения об успехе */
}

/* Определение классов для рамки */
.yellow-border {
  border-color: yellow; /* Желтая рамка */
}

.blue-border {
  border-color: blue; /* Синяя рамка */
}

.red-border {
  border-color: red; /* Красная рамка */
}

.centered-title {
  text-align: center; /* Центрируем текст */
  margin-bottom: 20px; /* Отступ снизу для заголовка */
}
</style>
