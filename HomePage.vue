<template>
  <ion-page>
    <ion-content :fullscreen="true" class="ion-padding">
      <!-- Bagian Tombol Refresh -->
      <div class="refresh-container">
        <ion-button color="primary" class="btn-refresh" @click="fetchCryptoData">
          Refresh
        </ion-button>
      </div>

      <!-- Indikator Loading saat mengambil data -->
      <div v-if="isLoading" class="loading-box">
        <ion-spinner name="crescent"></ion-spinner>
        <p>Memuat data cryptocurrency...</p>
      </div>

      <!-- Daftar Data Cryptocurrency -->
      <div v-else class="crypto-list">
        <div v-for="coin in cryptoList" :key="coin.id" class="crypto-row">
          <!-- Kolom 1: Rank -->
          <div class="col-rank">
            <div class="label-small">Rank</div>
            <div class="value-large">{{ coin.rank }}</div>
          </div>

          <!-- Kolom 2: Name & Symbol -->
          <div class="col-name">
            <div class="label-small">{{ coin.name }}</div>
            <div class="value-large">{{ coin.symbol }}</div>
          </div>

          <!-- Kolom 3: USD & Price -->
          <div class="col-price">
            <div class="label-small">USD</div>
            <div class="value-large">{{ coin.price_usd }}</div>
          </div>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { IonPage, IonContent, IonButton, IonSpinner } from '@ionic/vue';

// Interface untuk mendefinisikan struktur data dari API Coinlore
interface CryptoItem {
  id: string;
  rank: number;
  name: string;
  symbol: string;
  price_usd: string;
}

const cryptoList = ref<CryptoItem[]>([]);
const isLoading = ref<boolean>(false);

// Fungsi untuk mengambil data dari REST API Coinlore
const fetchCryptoData = async () => {
  isLoading.value = true;
  try {
    const response = await fetch('https://api.coinlore.net/api/tickers/');
    const result = await response.json();
    
    // Data array berada di dalam properti 'data' pada respons JSON
    if (result && result.data) {
      cryptoList.value = result.data;
    }
  } catch (error) {
    console.error('Gagal mengambil data API:', error);
    alert('Terjadi kesalahan saat memuat data. Periksa koneksi internet Anda.');
  } finally {
    isLoading.value = false;
  }
};

// Jalankan fungsi fetchCryptoData secara otomatis saat halaman dibuka
onMounted(() => {
  fetchCryptoData();
});
</script>

<style scoped>
/* Container Tombol Refresh */
.refresh-container {
  display: flex;
  justify-content: center;
  margin-top: 12px;
  margin-bottom: 16px;
}

.btn-refresh {
  --border-radius: 8px;
  text-transform: none;
  font-weight: 500;
  width: 120px;
  height: 42px;
}

/* Loading state */
.loading-box {
  text-align: center;
  margin-top: 40px;
  color: #555;
}

/* Tabel / Daftar Crypto sesuai desain soal */
.crypto-list {
  border: 1px solid #d4b975;
  background-color: #fdf0c7;
}

.crypto-row {
  display: flex;
  align-items: center;
  padding: 10px 12px;
  border-bottom: 1px solid #d4b975;
  color: #000000;
}

.crypto-row:last-child {
  border-bottom: none;
}

/* Proporsi 3 Kolom */
.col-rank {
  width: 22%;
  text-align: center;
}

.col-name {
  width: 38%;
  text-align: left;
  padding-left: 8px;
}

.col-price {
  width: 40%;
  text-align: left;
}

/* Tipografi Teks Atas (Kecil) dan Bawah (Besar) */
.label-small {
  font-size: 13px;
  color: #222222;
  margin-bottom: 2px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.value-large {
  font-size: 21px;
  font-weight: 500;
  color: #000000;
  line-height: 1.2;
}
</style>