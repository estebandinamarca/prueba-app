<template>
  <div class="main">

    <!-- Nav -->
    <nav class="py-3">
      <div
        v-for="(raza, index) in razas"
        :key="index">
        <button
          @click="toggleFilter(raza.nombre)"
          :class="{ active : selected.includes(raza.nombre) }"
          type="button"
          class="list-group-item list-group-item-action rounded-0 border-0 py-2 pl-4 pr-2"><i class="la la-check"></i> {{ raza.nombre }}</button>
          <div
            v-for="(subraza, index) in raza.subraza"
            :key="index">
            <button
              @click="toggleFilter(subraza.raza)"
              :class="{ active : selected.includes(subraza.raza) }"
              type="button"
              class="list-group-item list-group-item-action border-0 rounded-0 py-2 px-4 pl-5 pr-2"><i class="la la-check"></i> {{ subraza.nombre }}</button>
          </div>
      </div>

    </nav>

    <!-- Main Content -->
    <main class="py-4">
      <div class="container-fluid">

        <!-- Title -->
        <div class="row">
          <div class="col">
            <h1 class="m-0 py-3">Razas de Perros</h1>
          </div>
        </div>

        <!-- Selected Items -->
        <div class="row py-2 pb-5">
          <div
            v-if="!loading"
            class="col">
            <button
              v-for="(filter, index) in selected"
              :key="index"
              @click="toggleFilter(filter)"
              type="button"
              class="btn btn-light mr-3 my-2">
              {{ filter }} <span class="badge badge-light"><i class="la la-close"></i></span>
            </button>
          </div>
          <div
            v-if="loading"
            class="col">
              <p class="text-center py-5">Cargando...</p>
          </div>
        </div>

        <!-- List razasFiltered -->
        <div class="row">
          <div
            v-for="(raza, index) in razasFiltered"
            :key="'raza-' + index"
            class="col-12 col-md-4">
            <div class="w-100 position-relative mb-4">
              <div class="name w-100 h5 m-0 py-4 px-3">
                {{ raza.nombre }}
              </div>
              <div
                :style="{ backgroundImage: 'url(' + raza.img + ')' }"
                class="img w-100"
                style="min-height: 200px;">
              </div>
            </div>
          </div>
        </div>

      </div>
    </main>

  </div>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'
import env from '../global/config'

export default {
  data() {
    return {
      data: [],
      razas: [],
      selected: [],
      loading: false
    }
  },
  computed: {
    razasFiltered() {
      if (!this.selected.length) {
        return this.razas
      } else {
        return this.razas.filter(raza => this.selected.includes(raza.nombre))
      }
    }
  },
  mounted() {
    this.getRazas()
  },
  methods: {
    toggleFilter(raza) {
      if (!this.selected.includes(raza)) {
        this.selected.push(raza)
      } else {
        this.selected.splice(this.selected.indexOf(raza), 1)
      }
    },
    getRazas() {
      const _this = this
      _this.razas.subraza = []
      this.loading = true
      // Get razas
      return axios.get(`${env.endpoint}/breeds/list/all`).then((response) => {
        this.data = response.data.message
        _.forEach(this.data, function (subrazas, raza) {
          const _raza = {
            'nombre': _.capitalize(raza),
            'raza': _.capitalize(raza),
            'img': [],
            'subraza': []
          }
          // Get images
          return axios.get(`${env.endpoint}/breed/${raza}/images/random`).then((response) => {
            const razaImg = response.data.message
            _raza.img.push(razaImg)

            if (subrazas.length) {
              _.forEach(subrazas, function (subraza) {
                const _subraza = {
                  'nombre': _.capitalize(subraza),
                  'raza': _.capitalize(raza),
                  'img': undefined
                }
                _raza.subraza.push(_subraza)
              })
              _this.razas.push(_raza)
            } else {
              _this.razas.push(_raza)
            }
          }).catch((err) => {
            console.log(err)
          })
        })
      }).catch((err) => {
        console.log(err)
      }).finally(() => {
        this.loading = false
      })
    }
  }
}
</script>

<style>
body {
  font-family: 'Jost';
}
nav {
  position: fixed;
  z-index: 2;
  top: 0;
  bottom: 0;
  left: 0;
  height: 100vh;
  width: 20%;
  background: white;
  overflow-y: scroll;
}

main {
  padding-left: 22%;
}

.img {
  background-size: cover;
  background-position: center center;
}

.name {
  position: absolute;
  bottom: 0;
  color: white;
  text-shadow: 0px 0px 5px rgba(0,0,0,0.72);

  background: rgba(0,0,0,0);
  background: -moz-linear-gradient(top, rgba(0,0,0,0) 0%, rgba(0,0,0,0.59) 100%);
  background: -webkit-gradient(left top, left bottom, color-stop(0%, rgba(0,0,0,0)), color-stop(100%, rgba(0,0,0,0.59)));
  background: -webkit-linear-gradient(top, rgba(0,0,0,0) 0%, rgba(0,0,0,0.59) 100%);
  background: -o-linear-gradient(top, rgba(0,0,0,0) 0%, rgba(0,0,0,0.59) 100%);
  background: -ms-linear-gradient(top, rgba(0,0,0,0) 0%, rgba(0,0,0,0.59) 100%);
  background: linear-gradient(to bottom, rgba(0,0,0,0) 0%, rgba(0,0,0,0.59) 100%);
  filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#000000', endColorstr='#000000', GradientType=0 );
}

button:focus,
.btn:focus {
  outline: none;
}

.list-group-item.active {
  color: #212529;
  background-color: #E9E9E9;
  border-color: #E9E9E9;
}
.list-group-item .la {
  color: #E9E9E9
}
.list-group-item.active .la {
  color: #212529;
}
</style>
