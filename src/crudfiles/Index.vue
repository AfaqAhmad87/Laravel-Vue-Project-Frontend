<script setup>
import { onMounted, ref } from 'vue';

const display = ref([]);
const error = ref('');
const loading = ref(true);

onMounted(async () => {
  fetch("http://127.0.0.1:8000/api/getProfiles")
    .then((res) => res.json())
    .then((res) => {
      display.value = res;
      loading.value = false;
    })
    .catch(() => (error.value = `Failed To Fetch Values`));
});
</script>

<template>
  <div class="container">
    <h1 class="title">Profiles</h1>

    <h1 v-if="loading" class="loading">Loading...</h1>

    <div v-else>
      <h1 v-if="error" class="error">{{ error }}</h1>

      <div v-else>
        <table class="profile-table">
          <thead>
            <tr>
              <th>Name</th>
              <th>Description</th>
              <th>Age</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in display" :key="item.id">
              <td>{{ item.name }}</td>
              <td>{{ item.description }}</td>
              <td>{{ item.age }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 900px;
  margin: 2rem auto;
  padding: 1.5rem;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.title {
  text-align: center;
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 1.5rem;
  color: #333;
}

.loading {
  text-align: center;
  color: #777;
  font-size: 1.2rem;
}

.error {
  text-align: center;
  color: #d9534f;
  font-weight: bold;
  margin-bottom: 1rem;
}

.profile-table {
  width: 100%;
  border-collapse: collapse;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.05);
  border-radius: 8px;
  overflow: hidden;
}

.profile-table th,
.profile-table td {
  border: 1px solid #e0e0e0;
  padding: 12px 16px;
  text-align: left;
}

.profile-table th {
  background-color: #0077cc;
  color: white;
  font-weight: 600;
  text-transform: uppercase;
  font-size: 0.9rem;
}

.profile-table tr:nth-child(even) {
  background-color: #f9f9f9;
}

.profile-table tr:hover {
  background-color: #eef6ff;
  transition: background-color 0.2s ease;
}

.profile-table td {
  color: #333;
  font-size: 0.95rem;
}
</style>
