<template>
  <BasicLayout>
    <div class="ui message">
    <div class="header">
      Ecommerce ficticia
    </div>
      <p>Bienvenido, esta una tienda ficticia para comprobar funcionalidades los productos añadidos se borranran automáticamente pasado un periódo de tiempo. Para añadir productos y ver las funcionalidades de la tienda has de acceder al gestor donde has de subir productos con una imagen y una categoria. Este es el <a href="https://ecommerce-strapi-mg.herokuapp.com/admin/auth/login">enlace</a> al gestor donde has de acceder con las siguientes credenciales:</p>
      <p>Correo electrónico: admin@admin.com</p>
      <p>Contraseña: Admin1234</p>
    </div>
    <h1>Últimos productos</h1>
    <div class="ui grid">
      <div class="sixten wide mobile eight wide tablet four wide computer column" v-for="product in products" :key="product.id">
        <Product :product="product"/>
      </div>
    </div>
  </BasicLayout>
</template>

<script>
import { ref, onMounted } from 'vue';
import BasicLayout from '../layouts/BasicLayout.vue';
import { getProducts } from '../api/product';
import Product from '../components/Product.vue';

  export default {
    name: 'Home',

    components: {
      BasicLayout,
      Product,
    },

    setup() {
      let products = ref(null);
      
      onMounted(async () => {
        const response = await getProducts(20);
        products.value = response;
      });

      return{
        products,
      }
    }

  }
</script>