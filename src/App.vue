<script setup>
  import { ref, computed } from "vue";
  import axios from "axios";
  
  const url = "http://localhost:3000";
  
  const product = ref([]);
  
  let nama_product = ref("");
  let category_product = ref("");
  let price_product = ref(0);
  let quantity_product = ref(0);
  
  function get_product() {
  axios.get(url + '/products').then((response) => {
    console.log(response.data);
    product.value = [];
    let data_product = response.data.data.products;
    data_product.forEach((item) => {
      product.value.push(item);
    });
    console.log(product);
  });
  };
  
  
  function simpan_product( ){
    
    axios.post(url + '/products', {
      name: nama_product.value,
      category: category_product.value,
      price: price_product.value,
      quantity: quantity_product.value,
    }).then((response) => {
      console.log(response.data);
    get_product();
  }).catch((error) => {
    console.log(error);
  });

};

function delete_product(id){
  axios.delete(url + '/products/' + id).then((response) => {
    console.log(response.data);
    get_product();
  }).catch((error) => {
    console.log(error);
  });
};


/* cari product */

const search_product = ref("");

const filteredProducts = computed(() => {
  return product.value.filter(p => {
    return p.name.toLowerCase().includes(search_product.value.toLowerCase())
      || p.category.toLowerCase().includes(search_product.value.toLowerCase())
      || p.price.toString().includes(search_product.value)
      || p.quantity.toString().includes(search_product.value)
  });
});

/* script button add */
const show_form_add = ref(false); // Add this line


get_product();
</script>

<template>
  <div class=" p-12 bg-gray-100">
    <!-- Judul -->
    <div class=" mt-14">
      <h1 class="text-4xl font-bold text-center">Mini Project S4</h1>
    </div>
    <!-- Button Show From Add -->
    <div class=" px-12">
      <!-- tamabhakan code agar button bisa di buka -->
      <button
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
        v-on:click="show_form_add = !show_form_add"
        type="button"
      >
        Add Product
      </button>
    </div>
    <!-- From Add -->
    <div v-if="show_form_add" class="mt-10 px-12">
      <form action=""   >
        <div class="mb-4">
          <label class="block text-gray-700 font-bold mb-2" for="name">
            Name
          </label>
          <input
            class="shadow appearance-none border rounded w-1/2 py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
            id="name"
            type="text"
            placeholder="Enter name"
            v-model="nama_product"
          />
        </div>
        <div class="mb-4">
          <label class="block text-gray-700 font-bold mb-2" for="category">
            Category
          </label>
          <input
            class="shadow appearance-none border rounded w-1/2 py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
            id="category"
            type="text"
            placeholder="Enter category"
            v-model="category_product"
          />
        </div>
        <div class="mb-4">
          <label class="block text-gray-700 font-bold mb-2" for="price">
            Price
          </label>
          <input
            class="shadow appearance-none border rounded w-1/2 py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
            id="price"
            type="text"
            placeholder="Enter price"
            v-model="price_product"
          />
        </div>
        <div class="mb-4">
          <label class="block text-gray-700 font-bold mb-2" for="quantity">
            Quantity
          </label>
          <input
            class="shadow appearance-none border rounded w-1/2 py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
            id="quantity"
            type="text"
            placeholder="Enter quantity"
            v-model="quantity_product"
          />
        </div>
        <div class="flex items-center justify-between w-1/2">
          <template v-if="product.id == ''">
            <button v-on:click="simpan_product" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">
              Save Product
            </button>
            <button class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" type="reset">
              Reset
            </button>
          </template>
          <template v-else>
            <button v-on:click="editProduct" class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">
              Edit Product
            </button>
            <button class="bg-gray-500 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" type="button" 
            v-on:click="product = { id: '', name: '', category: '', price: 0, quantity: 0 }">
              Cancel
            </button>
          </template>
        </div>
        
      </form>
    </div>
    <!-- Cari -->
    <div class="mt-10 px-12">
      <input
        class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
        id="cari"
        type="text"
        placeholder="Search"
        v-model="search_product"
      />
    </div>
    <!-- Table -->
    <div class=" p-12 ">
      <table class="table-auto w-full">
        <thead class="sticky top-0">
          <tr class="bg-gray-200 text-gray-600 uppercase text-sm leading-normal">
            <th class="px-4 py-2">ID</th>
            <th class="px-4 py-2">Name</th>
            <th class="px-4 py-2">Category</th>
            <th class="px-4 py-2">Price</th>
            <th class="px-4 py-2">Quantity</th>
            <th class="px-4 py-2">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="items in filteredProducts" >
            <td class="border px-4 py-2">{{ items.id }}</td>
            <td class="border px-4 py-2">{{ items.name }}</td>
            <td class="border px-4 py-2">{{ items.category  }}</td>
            <td class="border px-4 py-2">{{ items.price  }}</td>
            <td class="border px-4 py-2">{{ items.quantity  }}</td>
            <td class="border px-4 py-2">
              <button v-on:click=""  class="text-sm bg-green-500 hover:bg-green-700 text-white py-1 px-2 rounded focus:outline-none focus:shadow-outline">Edit</button>
              <button v-on:click="delete_product(items.id)" class=" ml-2 text-sm bg-red-500 hover:bg-red-700 text-white py-1 px-2 rounded focus:outline-none focus:shadow-outline">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style scoped>

</style>
