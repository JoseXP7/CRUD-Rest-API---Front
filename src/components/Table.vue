<script setup>
import api from '../config/axios'
import { onMounted, ref } from 'vue'
import Swal from 'sweetalert2'

let languages = ref([])
let language = ref()
let programmers = ref()

let uniqueLanguages = ref([])
let uniqueID = ref()
let uniqueName = ref()
let uniqueProgrammers = ref()

const addLanguage = async () => {
  if (
    language.value == undefined ||
    programmers.value == undefined ||
    language.value == '' ||
    programmers.value == ''
  ) {
    Swal.fire({
      icon: 'error',
      title: 'Oops...',
      text: 'Rellena los campos',
    })
  } else {
    try {
      await api.post('/', {
        name: language.value,
        programmers: programmers.value,
      })
      Swal.fire({
        icon: 'success',
        title: 'Añadido correctamente',
        showConfirmButton: false,
        timer: 1500,
      })
      getLanguages()
      language.value = ''
      programmers.value = ''
    } catch (error) {
      console.error(error)
    }
  }
}

const getLanguages = async () => {
  try {
    const { data } = await api.get('/')
    languages.value = data
  } catch (error) {
    console.error(error)
  }
}

const getUniqueLanguage = async (id) => {
  try {
    const { data } = await api.get(`${id}`)
    uniqueLanguages = data
    uniqueLanguages.forEach((l) => {
      uniqueID.value = l.id
      uniqueName.value = l.name
      uniqueProgrammers.value = l.programmers
    })
  } catch (error) {
    console.error(error)
  }
}

const updateLanguage = async (id) => {
  if (
    uniqueName.value == '' ||
    uniqueProgrammers.value == '' ||
    uniqueName.value == null ||
    uniqueProgrammers.value == null
  ) {
    Swal.fire({
      icon: 'error',
      title: 'Oops...',
      text: 'Rellena los campos',
    })
  } else {
    try {
      await api.put(`/${id}`, {
        name: uniqueName.value,
        programmers: uniqueProgrammers.value,
      })
      getLanguages()
    } catch (error) {
      console.error(error)
    }
  }
}

const deleteLanguage = async (id) => {
  try {
    await api.delete(`/${id}`)
    Swal.fire({
      icon: 'success',
      title: 'Eliminado correctamente',
      showConfirmButton: false,
      timer: 1500,
    })
    getLanguages()
  } catch (error) {
    console.error(error)
  }
}

onMounted(() => {
  getLanguages()
})
</script>

<template>
  <div class="col-md-6">
    <form @submit.prevent="onSubmit">
      <div class="mb-3 d-flex align-items-center gap-3">
        <div>
          <label class="form-label" for="language">Lenguaje</label>
          <input
            class="form-control"
            type="text"
            name="language"
            id="language"
            v-model="language"
          />
        </div>
        <div>
          <label class="form-label" for="programmers">Programadores</label>
          <input
            class="form-control"
            type="number"
            name="programmers"
            id="programmers"
            v-model="programmers"
          />
        </div>
      </div>
      <button class="btn btn-primary" @click="addLanguage">
        Añadir Lenguaje
      </button>
    </form>
  </div>
  <div class="table-responsive">
    <table class="table table-striped">
      <thead>
        <tr>
          <th>ID</th>
          <th>Lenguaje</th>
          <th>Programadores</th>
          <th>Acción</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="language in languages" :key="language.id">
          <th>{{ language.id }}</th>
          <td>{{ language.name }}</td>
          <td>{{ language.programmers }}</td>
          <td>
            <div class="d-flex gap-2">
              <button
                class="btn btn-warning"
                @click="getUniqueLanguage(language.id)"
                data-bs-toggle="modal"
                data-bs-target="#exampleModal"
              >
                <i class="bi bi-pencil-square"></i>
              </button>
              <button
                class="btn btn-danger"
                @click="deleteLanguage(language.id)"
              >
                <i class="bi bi-trash"></i>
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
  <div
    class="modal fade"
    id="exampleModal"
    tabindex="-1"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
  >
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">
            Modificar Lenguaje
          </h1>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <h4>ID: {{ uniqueID }}</h4>
          <div class="col-6">
            <input class="form-control mb-3" type="text" v-model="uniqueName" />
            <input
              class="form-control"
              type="number"
              v-model="uniqueProgrammers"
            />
          </div>
        </div>
        <div class="modal-footer">
          <button
            type="button"
            class="btn btn-secondary"
            data-bs-dismiss="modal"
          >
            Cancelar
          </button>
          <button
            type="button"
            class="btn btn-primary"
            @click="updateLanguage(uniqueID)"
            data-bs-dismiss="modal"
          >
            Guardar Cambios
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
