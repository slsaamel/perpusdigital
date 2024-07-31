<template>
  <div class="container-fluid">
    <div class="row content">
      <div class="col-lg-12">
        <h2 class="text-center my-4 just" style="color: aliceblue">RAK BUKU</h2>
        <div class="row d-flex justify-content-center">
          <nuxt-link to="/">
            <i class="bi bi-caret-left-fill fs-1"></i>
          </nuxt-link>
          <div class="col-lg-3">
            <div class="my-3">
              <select v-model="keyword" type="search" class="form-select" aria-label="seacrh" style="box-shadow: 2px px px px px #424242" @input="getBooks">
                <option value="" disabled>...</option>
                <option v-for="(kategori, i) in kategories" :key="i" :value="kategori.nama">{{ kategori.nama }}</option>
              </select>
            </div>
          </div>
          <form @submit.prevent="getBooks">
            <div class="col-lg-8">
              <input v-model="keyword" type="search" class="form-control rounded-5 cari" placeholder="SEARCH?..." style="background-color: #b0cfe5" />
            </div>
          </form>
        </div>
        <div class="my-3 text-muted"></div>
        <div class="row layer m-5 rounded-5">
          <h1 class="text-center" style="color: aliceblue">Koleksi Buku</h1>
          <div v-for="(book, i) in books" :key="i" class="col-lg-2">
            <div class="card mb-3">
              <div class="card-body">
                <nuxt-link :to="`/buku/${book.id}`">
                  <img :src="book.cover" class="cover" alt="cover 1" style="width: 100%" />
                </nuxt-link>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const supabase = useSupabaseClient();
const keyword = ref("");
const books = ref([]);
const kategories = ref([]);

const getBooks = async () => {
  const { data, error } = await supabase.from("buku").select(`* ,kategori_buku(*)`).ilike("judul", `%${keyword.value}%`);
  if (error) throw error;
  if (data) books.value = data;
};

const getKategori = async () => {
  const { data, error } = await supabase.from("kategori_buku").select("*");
  if (data) kategories.value = data;
};

const bookFiltered = computed(() => {
  return books.value.filter((b) => {
    return b.judul?.toLowerCase().includes(keyword.value?.toLowerCase()) || b.kategori?.nama.toLowerCase().includes(keyword.value?.toLowerCase());
  });
});

onMounted(() => {
  getBooks();
  getKategori();
});
</script>

<style scoped>
.content {
  background-color: #d9d9d9;
}

/* .cari{
    width: 40rem;
} */
/* .cover{
    height: 50%;
    width: 50%;
} */
.layer {
  background-color: #d9d9d9;
}

h2 {
  color: white;
  font-family: "Irish Grover", system-ui;
}

h1 {
  color: white;
  font-family: "Irish Grover", system-ui;
}

.card {
  height: 250px;
}

.bi-caret-left-fill {
  margin-left: 50px;
}
</style>
