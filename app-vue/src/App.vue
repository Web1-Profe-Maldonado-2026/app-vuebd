<script setup lang="ts">
import { ref, onMounted } from 'vue'

// Contrato de datos con TypeScript
interface Producto {
  id: number
  nombre: string
  email: string
  creado_en: string // mysql devuelve las fechas como texto (string)
}

// Estados reactivos
const productos = ref<Producto[]>([])
const cargando = ref<boolean>(true)
const errorMensaje = ref<string>('')

// Función asíncrona para consultar el PHP en Railway
const obtenerProductos = async () => {
  try {
    const respuesta = await fetch('https://php-cors-production.up.railway.app/api_productos_pdo.php')
    if (!respuesta.ok) throw new Error('Servidor inaccesible')
    
    productos.value = await respuesta.json()
  } catch (err: any) {
    errorMensaje.value = 'No se pudo conectar a la API en Railway. Revisa el servidor.'
  } finally {
    cargando.value = false
  }
}

// Se ejecuta al montar el componente en el navegador
onMounted(() => {
  obtenerProductos()
})
</script>

<template>
  <div class="container mt-5">
    <h2>📦 Despliegue de la información de Usuarios (Vue + PHP + Railway)</h2>
    <hr />

    <!-- Estado de Carga -->
    <div v-if="cargando" class="alert alert-info">
      🔄 Cargando datos desde el backend PHP...
    </div>

    <!-- Estado de Error -->
    <div v-else-if="errorMensaje" class="alert alert-danger">
      ⚠️ {{ errorMensaje }}
    </div>

    <!-- Renderizado de Datos -->
    <table v-else class="table table-striped table-hover border">
      <thead class="table-dark">
        <tr>
          <th>ID</th>
          <th>Nombre</th>
          <th>Correo</th>
          <th>Fecha de Creación/Hora</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="prod in productos" :key="prod.id">
          <td>{{ prod.id }}</td>
          <td>{{ prod.nombre }}</td>
          <td>{{ prod.email }}</td>
          <td>{{ prod.creado_en }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>