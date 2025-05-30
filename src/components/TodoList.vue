<script setup>
import { ref, computed, onMounted, watch } from 'vue'

const inputNamaMahasiswa = ref(null)
const namaBaru = ref('')
const alamatBaru = ref('')
const daftarMahasiswa = ref([])
const kataKunci = ref('')
const editId = ref(null)

const totalMahasiswa = computed(() => daftarMahasiswa.value.length)

const daftarTampil = computed(() => {
  if (!kataKunci.value.trim()) return daftarMahasiswa.value
  return daftarMahasiswa.value.filter(m =>
    m.nama.toLowerCase().includes(kataKunci.value.toLowerCase()) ||
    m.alamat.toLowerCase().includes(kataKunci.value.toLowerCase())
  )
})

const tambahMahasiswa = () => {
  if (!namaBaru.value.trim()) return
  if (editId.value) {
    // Mode edit
    const index = daftarMahasiswa.value.findIndex(m => m.id === editId.value)
    if (index !== -1) {
      daftarMahasiswa.value[index].nama = namaBaru.value
      daftarMahasiswa.value[index].alamat = alamatBaru.value.trim() || 'Alamat tidak diinputkan'
    }
    editId.value = null
  } else {
    daftarMahasiswa.value.push({
      id: Date.now(),
      nama: namaBaru.value,
      alamat: alamatBaru.value.trim() || 'Alamat tidak diinputkan',
    })
  }
  namaBaru.value = ''
  alamatBaru.value = ''
  inputNamaMahasiswa.value.focus()
}

const hapusMahasiswa = (id) => {
  daftarMahasiswa.value = daftarMahasiswa.value.filter(m => m.id !== id)
}

const editMahasiswa = (mahasiswa) => {
  namaBaru.value = mahasiswa.nama
  alamatBaru.value = mahasiswa.alamat
  editId.value = mahasiswa.id
  inputNamaMahasiswa.value.focus()
}

const hapusSemua = () => {
  if (confirm('Yakin ingin menghapus semua data mahasiswa?')) {
    daftarMahasiswa.value = []
  }
}

onMounted(() => {
  inputNamaMahasiswa.value.focus()
})

watch(namaBaru, (baru, lama) => {
  console.log(`Nama berubah dari "${lama}" ke "${baru}"`)
})
</script>

<template>
  <div class="form-wrapper">
    <h2>Todo-List Absensi</h2>

    <input
      v-model="namaBaru"
      ref="inputNamaMahasiswa"
      placeholder="Nama mahasiswa"
      class="form-input"
    />
    <input
      v-model="alamatBaru"
      placeholder="Alamat mahasiswa"
      class="form-input"
    />
    <button @click="tambahMahasiswa" class="form-button">
      {{ editId ? 'Update' : 'Daftar' }}
    </button>

    <input
      v-model="kataKunci"
      placeholder="Cari nama/alamat..."
      class="form-input"
    />

    <ol>
      <li v-for="mahasiswa in daftarTampil" :key="mahasiswa.id" class="student-item">
        {{ mahasiswa.nama }} - {{ mahasiswa.alamat }}
        <button @click="editMahasiswa(mahasiswa)" class="edit-button">Edit</button>
        <button @click="hapusMahasiswa(mahasiswa.id)" class="delete-button">Hapus</button>
      </li>
    </ol>

    <p>Total mahasiswa: {{ totalMahasiswa }}</p>
    <button @click="hapusSemua" class="clear-button">Hapus Semua</button>
  </div>
</template>

<style scoped>
.form-wrapper {
  max-width: 400px;
  margin: 50px auto;
  font-family: Arial, sans-serif;
  background: #fff;
  padding: 0;
  text-align: center;
}
h2 {
  margin-bottom: 20px;
  color: #333;
}
.form-input {
  width: 100%;
  padding: 8px 10px;
  margin-bottom: 12px;
  border: 1px solid black;
  box-sizing: border-box;
}
.form-button {
  width: 100%;
  padding: 10px 0;
  background: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
  font-weight: bold;
}
.form-button:hover {
  background: #45a049;
}
ol {
  padding-left: 40px;
  color: #555;
  font-size: 16px;
  text-align: left;
}
.student-item {
  margin-bottom: 8px;
}
.delete-button, .edit-button {
  margin-left: 5px;
  padding: 3px 8px;
  border: none;
  color: white;
  cursor: pointer;
}
.delete-button {
  background: #e74c3c;
}
.delete-button:hover {
  background: #c0392b;
}
.edit-button {
  background: #3498db;
}
.edit-button:hover {
  background: #2980b9;
}
.clear-button {
  margin-top: 10px;
  padding: 10px 0;
  width: 100%;
  background: #f39c12;
  color: white;
  border: none;
  cursor: pointer;
  font-weight: bold;
}
.clear-button:hover {
  background: #e67e22;
}
p {
  margin-top: 20px;
  color: #333;
  font-weight: 600;
}
</style>
