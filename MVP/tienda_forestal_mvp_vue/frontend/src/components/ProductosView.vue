<template>
  <div class="productos-view">

    <!-- ===============================
         TÍTULO
         =============================== -->
    <h2>Catálogo de Productos (MVP)</h2>

    <!-- ===============================
         BÚSQUEDA
         =============================== -->
    <div class="busqueda">
      <input
        type="text"
        v-model="terminoBusqueda"
        placeholder="¿Qué producto estás buscando?"
      />
      <button @click="accionEncontrar">Buscar</button>
    </div>

    <!-- ===============================
         FILTROS
         =============================== -->
    <div class="filtros">
        <input v-model="filtroMarca" placeholder="Marca">
        <input v-model="filtroTipo" placeholder="Tipo">
        <button @click="accionEncontrar">Aplicar filtros</button>
    </div>

    <!-- ===============================
         LISTADO DE PRODUCTOS
         =============================== -->
    <div v-if="loading" class="loading">
      Cargando productos...
    </div>

    <div v-else class="grid">
      <div
        v-for="producto in productos"
        :key="producto.id"
        class="card"
      >
        <!-- Imagen del producto -->
        <img
          :src="getImagen(producto.imagen)"
          :alt="producto.nombre"
        />

        <h3>{{ producto.nombre }}</h3>
        <p>{{ producto.descripcion }}</p>
        <strong>{{ producto.precio }} euros</strong>
      </div>
    </div>

    <!-- ===============================
         PAGINACIÓN
         =============================== -->
    <div class="paginacion" v-if="totalPaginas > 1">

      <button
        @click="paginaAnterior"
        :disabled="paginaActual === 1"
      >
        ← Anterior
      </button>

      <span>
        Página {{ paginaActual }} de {{ totalPaginas }}
      </span>

      <button
        @click="paginaSiguiente"
        :disabled="paginaActual === totalPaginas"
      >
        Siguiente →
      </button>

    </div>

  </div>
</template>

<script setup>
/*
=================================================
IMPORTS
=================================================
*/
import { onMounted } from 'vue'
import ProductsPresenter from '@/presenters/ProductsPresenter'

/*
=================================================
PRESENTER
=================================================
Creamos el Presenter.
La View NO sabe cómo se cargan los datos,
solo usa lo que el Presenter expone.
*/
const presenter = ProductsPresenter()

/*
=================================================
DESTRUCTURING
=================================================
Extraemos solo lo que la View necesita.
Esto refuerza la separación MVP.
*/
const {
  productos,
  loading,
  terminoBusqueda,
  filtroMarca,
  filtroTipo,
  paginaActual,
  totalPaginas,
  cargarProductos,
  buscarProductos,
  paginaSiguiente,
  paginaAnterior
} = presenter

/*
=================================================
EVENTOS DE LA VIEW
=================================================
*/
const accionEncontrar = () => {
  buscarProductos()
}

/*
=================================================
IMÁGENES
=================================================
La View decide cómo mostrar las imágenes.
No es responsabilidad del Presenter ni del backend.
*/
const getImagen = (nombreImagen) => {
  return new URL(`../assets/img/${nombreImagen}`, import.meta.url).href
}

/*
=================================================
CICLO DE VIDA
=================================================
Al montar la vista, pedimos al Presenter
que cargue los productos.
*/
onMounted(() => {
  cargarProductos()
})
</script>

<style scoped>
.productos-view {
  padding: 1rem;
}

.busqueda {
  margin-bottom: 1rem;
  display: flex;
  gap: 0.5rem;
}

.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 2rem;
}

.card {
  background: #e8f5e9;
  padding: 1rem;
  border-radius: 8px;
  box-shadow: 0 0 8px rgba(0, 128, 0, 0.3);
}

.card img {
  width: 100%;
  height: 150px;
  object-fit: cover;
}

.paginacion {
  margin-top: 1rem;
  display: flex;
  justify-content: center;
  gap: 1rem;
}

.paginacion button {
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 6px;
  cursor: pointer;
}

.button.activo {
  background-color: #1b5e20;
  color: #ffffff;
}

.loading {
  font-style: italic;
  color: #2e7d32;
}
</style>