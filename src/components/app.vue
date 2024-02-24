<template>
  <div class="container">
    <h1>Quản lý sản phẩm</h1>
    <table class="table table-bordered table-striped">
      <thead>
        <tr>
          <th>Ảnh</th>
          <th>Mã sản phẩm</th>
          <th>Tên sản phẩm</th>
          <th>Số lượng</th>
          <th>Giá</th>
          <th>Nhận xét</th>
          <th>Thao tác</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(product, index) in productList" :key="index">
          <td>
            <input type="file" v-if="product.editing" @change="handleImageChange(index, $event)" />
            <div v-else class="image-container">
              <img :src="require(`@/assets/images/${product.imageName || getImageFileName(product.image)}`)" alt="" />
            </div>
          </td>
          <td>
            <span v-if="!product.editing">{{ product.code }}</span>
            <input v-if="product.editing" v-model="product.code" />
          </td>
          <td>
            <span v-if="!product.editing">{{ product.name }}</span>
            <input v-if="product.editing" v-model="product.name" />
          </td>
          <td>
            <span v-if="!product.editing">{{ product.qty }}</span>
            <input type="number" v-if="product.editing" v-model="product.qty" />
          </td>
          <td>
            <span v-if="!product.editing">{{
              formatCurrency(product.price)
            }}</span>
            <input
              type="number"
              v-if="product.editing"
              v-model="product.price"
            />
          </td>
          <td>
            <select v-if="product.editing" v-model="product.rating">
              <option value="Mới">Mới</option>
              <option value="Đã qua SD">Đã qua SD</option>
              <option value="Như mới">Như mới</option>
            </select>
            <span v-else>{{ product.rating }}</span>
          </td>
          <td>
            <button type="button" class="btn btn-success mx-1" @click="addProduct">Thêm</button>

            <button class="btn btn-warning mx-1" @click="toggleEditing(index)">
              {{ product.editing ? "Lưu" : "Sửa" }}
            </button>
            <button class="btn btn-danger mx-1" @click="deleteProduct(index)">
              Xoá
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    
  </div>
</template>


<script setup>
import productListData from "./data.json";
import { ref } from "vue";

const productList = ref(getProductListFromStorage());

function getProductListFromStorage() {
  const storedData = localStorage.getItem("productList");
  return storedData ? JSON.parse(storedData) : productListData;
}

const formatCurrency = (value) => {
  return new Intl.NumberFormat("vi-VN", {
    style: "currency",
    currency: "VND",
  }).format(value);
};

const toggleEditing = (index) => {
  productList.value[index].editing = !productList.value[index].editing;
  saveData();
};
const deleteProduct = (index) => {
  productList.value.splice(index, 1);
  saveData();
};

const addProduct = () => {
  const newProduct = {
    image: "1.jpg",
    code: "",
    name: "",
    qty: 0,
    price: 0,
    rating: "Mới",
    editing: true
  };

  productList.value.unshift(newProduct);
  saveData();
};

const saveData = () => {
  localStorage.setItem("productList", JSON.stringify(productList.value));
};

const getImageFileName = (filePath) => filePath.split('/').pop();

const handleImageChange = (index, event) => {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    
    reader.onload = () => {
      productList.value[index].image = reader.result;
    };

    reader.readAsDataURL(file);

    productList.value[index].imageFile = file;
    productList.value[index].imageName = getImageFileName(file.name);

    saveData();
  }
};
</script>

<style>
.container {
  margin: 20px;
}
h1 {
  color: #3498db;
}
.image-container {
  width: 130px;
  height: 130px;
}
.image-container img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
</style>