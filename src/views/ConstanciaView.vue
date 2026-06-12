<template>
  <main class="constancia-container">
    <h1>Constancia de Matrícula</h1>
    
    <div v-if="loading" class="status">Cargando información...</div>
    <div v-else-if="error" class="status error">{{ error }}</div>
    
    <div v-else-if="results.length > 0">
      <section class="student-info">
        <h2>Datos del Estudiante</h2>
        <p><strong>CUI:</strong> {{ results[0].student.cui }}</p>
        <p><strong>Nombre:</strong> {{ results[0].student.full_name }}</p>
        <p><strong>Email:</strong> {{ results[0].student.email }}</p>
      </section>

      <section class="courses-info">
        <h2>Cursos Matriculados</h2>
        <table border="1" cellpadding="10" cellspacing="0">
          <thead>
            <tr>
              <th>Nº</th>
              <th>Código</th>
              <th>Curso</th>
              <th>Año</th>
              <th>Grupo</th>
              <th>Laboratorio</th>
              <th>Docente</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in results" :key="item.id">
              <td>{{ index + 1 }}</td>
              <td>{{ item.workload.course.code }}</td>
              <td>{{ item.workload.course.name }}</td>
              <td>{{ item.workload.course.year_display }}</td>
              <td>{{ item.workload.group }}</td>
              <td>{{ item.workload.laboratory }}</td>
              <td>{{ item.workload.teacher.full_name }}</td>
            </tr>
          </tbody>
        </table>
      </section>
    </div>
    
    <div v-else class="status">
      No se encontraron registros de matrícula para este CUI.
    </div>
  </main>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import axios from 'axios';

const route = useRoute();
const results = ref([]);
const loading = ref(true);
const error = ref(null);

onMounted(async () => {
  const cui = route.params.cui;
  const baseUrl = (import.meta.env.VITE_API_URL || '/api').replace(/\/$/, '');
  const backendUrl = `${baseUrl}/restful/enrollment-certificate/?cui=${cui}`;
  
  try {
    const response = await axios.get(backendUrl);
    results.value = response.data.results;
  } catch (err) {
    error.value = 'Hubo un error al contactar con el servidor. Verifica la URL de la API.';
    console.error(err);
  } finally {
    loading.value = false;
  }
});
</script>

<style scoped>
.constancia-container {
  max-width: 900px; /* Lo amplié un poco para que quepan mejor las nuevas columnas */
  margin: 0 auto;
  font-family: sans-serif;
}
.student-info {
  margin-bottom: 20px;
  padding: 15px;
  background-color: #f9f9f9;
  border-radius: 8px;
}
table {
  width: 100%;
  text-align: left;
  border-collapse: collapse;
}
th {
  background-color: #eee;
}
</style>