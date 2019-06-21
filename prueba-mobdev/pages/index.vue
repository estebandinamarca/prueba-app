<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <p
          v-for="(item, index) in list"
          :key="'breed_'+index">
          {{ item }} {{ index }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
const lunch = {
  sandwich: 'turkey',
  chips: 'Cape Cod',
  snack: 'Cookies',
  drink: 'Pepsi',
  calories: 325,
  picnic: true
}

export default {
  data() {
    return {
      list: lunch
    }
  },
  mounted() {
    this.traer()
  },
  methods: {
    traer() {
      console.log('hola')
      const alo = this.list
      alo.forEach(function (item, key) {
        console.log(key)
        console.log(item)
      })
    }
  }
}
</script>

<style>
</style>
