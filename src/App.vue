<script setup>
import { ref } from "vue";
const showForm = ref(false); // kondisi false agar nanti bisa muncul ketika true
const newMemo = ref(""); // kosong karena akan diisi ketika kita mengisi form dengan v-bind 2 arah v-model
const selectedCategory = ref(""); // untuk menyimpan kategori terpilih
const memos = ref([]); //menyimpan array dari data yang kita masukkan
const errorMessage = ref("");

const categories = ["Work", "Personal", "Shopping", "Others"]; // 4 kategori pilihan

function addMemo() {
  // Membuat error handlernya jika tidak ada catatan/kosong catatannya
  if (!newMemo.value) {
    errorMessage.value = "Masukkan catatan";
    return;
  }

  // mengisi note dengan data yang baru
  const timestamp = Date.now();
  memos.value.push({
    id: Date.now(),
    content: newMemo.value,
    category: selectedCategory.value, // tambahkan kategori terpilih
    backgroundColor: getColor(),
    date: new Date(timestamp).toDateString("id-ID", {
      day: "numeric",
      month: "long",
      year: "numeric",
    }),
  });
  // Setelah berhasil menambahkan value kosongin lagi notes nya
  newMemo.value = "";
  showForm.value = false;
  selectedCategory.value = ""; // reset kategori
}
function getColor() {
  const r = Math.floor(Math.random() * 100);
  const g = Math.floor(Math.random() * 100);
  const b = Math.floor(Math.random() * 100);
  return `#${r.toString(16).padStart(2, "0")}${g
    .toString(16)
    .padStart(2, "0")}${b.toString(16).padStart(2, "0")}`;
}

function deleteMemo(id) {
  memos.value = memos.value.filter((memo) => memo.id !== id);
}
</script>

<template>
  <main>
    <div class="container">
      <header>
        <!-- Mengubah nilai dari showform menjadi true untuk memunulkan overlay modal -->
        <h1 class="header-title">Memo Han</h1>
        <button @click="showForm = true" class="header-button">+</button>
      </header>
      <div class="card-container">
        <div
          v-for="(memo, index) in memos"
          :key="index"
          :style="{ backgroundColor: memo.backgroundColor }"
          class="card"
        >
          <p class="card-category">{{ memo.category }}</p>
          <p class="card-content">{{ memo.content }}</p>
          <div class="card-footer">
            <p class="card-date">{{ memo.date }}</p>
            <button @click="deleteMemo(memo.id)" class="card-button">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                stroke="currentColor"
                class="trash"
                width="24"
                height="24"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0"
                />
              </svg>
            </button>
          </div>
        </div>
      </div>
    </div>
    <!-- v-if untuk menghilangkan overlay karena false -->
    <div v-if="showForm" class="form-overlay">
      <div class="form-modal">
        <div class="form-modal-header">
          <h3>Isi Memo Anda</h3>
          <button @click="showForm = false" class="form-close-btn">
            &times;
          </button>
        </div>
        <p v-if="errorMessage" class="form-error">{{ errorMessage }}</p>
        <textarea
          v-model="newMemo"
          name="memo"
          id="memo"
          cols="30"
          rows="10"
        ></textarea>
        <select v-model="selectedCategory" class="selectedCategory">
          <option disabled value="">Pilih Kategori</option>
          <option
            v-for="category in categories"
            :key="category"
            :value="category"
          >
            {{ category }}
          </option>
        </select>
        <button @click="addMemo" class="form-save-btn">Save</button>
      </div>
    </div>
  </main>
</template>

<style scoped>
body {
  font-family: "Figtree", system-ui;
}
main {
  height: 100vh;
  width: 100vw;
}

.container {
  max-width: 900px;
  padding: 10px;
  margin: 0 auto;
}

/* Header */
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header-title {
  font-size: 3rem;
  font-weight: bold;
  margin-bottom: 2rem;
  color: #212529;
}

.header-button {
  border: none;
  border-radius: 100px;
  background-color: darkgreen;
  font-size: 1rem;
  padding: 0.5rem;
  width: 3rem;
  height: 3rem;
  color: white;
  cursor: pointer;
}

/* Card */
.card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}
.card {
  width: 225px;
  height: max-content;
  padding: 1.5rem;
  background-color: rgb(145, 145, 255);
  border-radius: 0.5rem;
  margin-bottom: 0.5rem 1rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  color: white;
}

.card-content {
  height: 100%;
  width: 100%;
}

.form-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
}

.form-modal {
  width: 30rem;
  background-color: white;
  border-radius: 0.5rem;
  padding: 2rem;
  position: relative;
  display: flex;
  flex-direction: column;
}

/* Category */
.card-category {
  font-size: 0.9rem;
  color: #ffdd57;
  font-weight: bold;
}

select {
  padding: 10px;
  margin-top: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
  width: 100%;
}

option {
  padding: 10px;
}

.form-modal-header {
  display: flex;
  align-items: center;
}

.form-error {
  color: red;
  padding: 0;
}

.form-save-btn {
  padding: 10px 20px;
  font-size: 1rem;
  width: 100%;
  background-color: #495057;
  border: none;
  cursor: pointer;
  border-radius: 5px;
  margin-top: 1rem;
  color: white;
}

.form-save-btn:hover {
  background-color: #37b24d;
}

.form-close-btn {
  position: absolute;
  top: 5px;
  right: 5px;
  width: 2rem;
  height: 2rem;
  background-color: #ffc9c9;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  color: #495057;
}

.form-close-btn:active:hover {
  background-color: #f79090;
}

.card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: white;
  font-weight: 600;
}

.card-button {
  background: none;
  border: none;
  cursor: pointer;
  padding: 5px;
}

.trash {
  color: white;
}
.trash:hover {
  color: red;
}
</style>
