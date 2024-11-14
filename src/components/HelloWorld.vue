<template>
  <v-container style="font-family: 'IranSansWeb', sans-serif">
    <v-data-table dir="rtl" class="v-table">
      <thead>
        <tr>
          <th class="text-right v-th">نام</th>
          <th class="text-right v-th">نام خانوادگی</th>
          <th class="text-right v-th">سن</th>
          <th class="text-right v-th">شغل</th>
          <th class="text-right v-th">توضیحات بیشتر</th>
          <th class="text-right v-th">ویرایش اطلاعات</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in items" :key="item.id">
          <td>{{ item.name }}</td>
          <td>{{ item.lastname }}</td>
          <td>{{ item.age }}</td>
          <td>{{ item.job }}</td>
          <td>
            <v-btn @click="openDetailDialog(item)">جزئیات</v-btn>
          </td>
          <td>
            <v-btn @click="editDialog(item)">ویرایش اطلاعات</v-btn>
          </td>
          
        </tr>
      </tbody>
    </v-data-table>

    <Userdialog :data="newItem" :isEditable="canEdit" :isNew=canAddNew v-model="dialog" @save="updateItem(newItem)" @saveNew="saveItem" />
    <v-row>
      <v-col cols="10"></v-col>
      <v-col cols="2">
        <v-btn @click="addNew()" style="margin-top: 5px; margin-left: 50px;">
          اضافه کردن
          <v-icon>fas fa-plus</v-icon>
        </v-btn>
      </v-col>
    </v-row>

    <v-snackbar v-model="showToastMessage" :timeout="2000" :color="toastColor" rounded="pill">
      {{ toastMessage }}
    </v-snackbar>
  </v-container>
</template>

<script setup lang="ts">

import Userdialog from './Userdialog.vue';
import { ref } from 'vue';

const UserDialog = Userdialog;

interface Item {
  id: number | string;
  name: string;
  lastname: string;
  age: string;
  job: string;
  gender: string;
  address: string;
  phonenumber: string;
  education: string;
}
loadItems()
const canAddNew = ref(false);
const canEdit = ref(false);
const showToastMessage= ref(false);
const toastMessage = ref('');
const toastColor = ref('success');
const dialog = ref(false);
const newItem = ref<Item>({ id: '', name: '', lastname: '', age: '', job: '', gender: '', address: '', phonenumber: '', education: '' });
const items = ref<Item[]>([]);

function showSuccessToast() {
  showToastMessage.value = true;
  toastMessage.value = 'انجام شد';
  toastColor.value = 'success';
}

function showErrorToast() {
  showToastMessage.value = true;
  toastMessage.value = 'اطلاعات را کامل وارد کنید';
  toastColor.value = 'error';
}

function saveItem() {
  if (!newItem.value.name) { 
    showErrorToast();
    return;
  }
  items.value.push({ ...newItem.value });
  saveItems();
  resetNewItem();
  dialog.value = false;
}

function resetNewItem() {
  newItem.value = { id: '', name: '', lastname: '', age: '', job: '', gender: '', address: '', phonenumber: '', education: '' };
}

function saveItems() {
  localStorage.setItem('items', JSON.stringify(items.value));
  showSuccessToast();
}

function loadItems() {
  const data = localStorage.getItem('items');
  if (data) {
    items.value = JSON.parse(data);
  }
}

function openDetailDialog(item: Item): void {
  canEdit.value = false;
  newItem.value = { ...item };
  dialog.value = true;
}

function editDialog(item: Item): void {
  newItem.value = { ...item };
  canEdit.value = true;
  dialog.value = true;
}

function addNew() {
  resetNewItem();
  canEdit.value = false;
  canAddNew.value = true;
  dialog.value = true;
}

function updateItem(newItem: Item) {
  const index = items.value.findIndex(i => i.id === newItem.id);
  if (index !== -1) {
    items.value.splice(index, 1, newItem);
    saveItems();
    dialog.value = false;
    showSuccessToast();
  } else {
    console.error("آیتم مورد نظر پیدا نشد");
  }
}
</script>

<style scoped>

</style>
