<script setup>
  import {ref,reactive,onMounted, watch} from 'vue'
  import {db} from './data/guitarras'
  //Componentes
  import Guitarra from './components/Guitarra.vue'
  import Header from './components/Header.vue'
  import Footer from './components/Footer.vue'
  // state reactive
  // const state = reactive({
  //   guitarras: [1,2,3]
  // })
  // console.log(state.guitarras)

  // state ref
  const guitarras = ref([])
  const carrito = ref([])
  const guitarra = ref({})

  // Ejecuta a hacer cambios
  watch(carrito,() =>{
    guardarLocalStorage()
  },{
    // observa cambios profundos 
    deep:true
  })

  // Metodo de ciclo de vida
  onMounted(() => {
    guitarras.value = db
    guitarra.value = db[3]

    const carritoStorage = localStorage.getItem('carrito')
    if (carritoStorage){
      //Convertir el json a arreglo 
      carrito.value = JSON.parse(carritoStorage)
    }

  })

  const guardarLocalStorage = ()=>{
    // Un objecto no puedo ser almacenado en localstorage
    // se usa el JSON.stringfy para convertirloa json y poder guardarlo 
    localStorage.setItem('carrito',JSON.stringify(carrito.value))
  }
// Funciónes de los eventos

  const agregarCarrito = (guitarra) => {
    const existeCarrito = carrito.value.findIndex(producto => producto.id == guitarra.id)
    if(existeCarrito >= 0){
      carrito.value[existeCarrito].cantidad++
    }else{
      guitarra.cantidad = 1
      carrito.value.push(guitarra)
    }
  }
  const decrementar = (id) =>{
    const index = carrito.value.findIndex(producto => producto.id == id)
    if(carrito.value[index].cantidad <= 1) return
      carrito.value[index].cantidad--
  }
  const incrementar = (id)=>{
    //Encuentra la posicion cuando la condicion se cumpla
    const index = carrito.value.findIndex(producto => producto.id == id)
    carrito.value[index].cantidad++
  }
  const eliminar = (id) =>{
    //Trae los registros segun la condicion 
    carrito.value = carrito.value.filter(producto => producto.id !== id)
  }
  const vacear = ()=>{
    carrito.value = []
  }


</script>

<template>

  <!-- Componente de header -->
  <Header 
    :carrito="carrito"
    :guitarra="guitarra"
    @incrementar="incrementar"
    @decrementar="decrementar"
    @agregar-carrito = "agregarCarrito"
    @eliminar="eliminar"
    @vacear="vacear"
  />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>
        <div class="row mt-5">
            <Guitarra 
            v-for="guitarra in guitarras"
            :key="guitarra.id"
            :guitarra="guitarra"
            @agregar-carrito = "agregarCarrito"
            />
        </div>
    </main>

    <Footer />

</template>
