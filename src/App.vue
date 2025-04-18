<script setup>
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'
import {ref,reactive, onMounted, watch} from 'vue'
import {db} from './data/guitarras'
import Guitarra from './components/Guitarra.vue'
//usando ref 
const guitarras=ref([])
const carrito =ref([])

const guitarra = ref({})

watch(carrito,()=>{
    guardarLocaStorage()
},{
    deep:true
})
onMounted(()=>{
    guitarras.value=db 
    guitarra.value=db[3]

    const carritoStorage= localStorage.getItem('carrito')
    if(carritoStorage){
        carrito.value= JSON.parse(carritoStorage)
    }
})

const agreagarCarrito=(guitarra)=>{
    const existeCarrito= carrito.value.findIndex(producto=>producto.id==guitarra.id)
    if(existeCarrito>=0){
        if(carrito.value[existeCarrito].cantidad>=5) return 
        carrito.value[existeCarrito].cantidad++
    }else{
        guitarra.cantidad=1
        carrito.value.push(guitarra)
    }
    
}

const decrementarCantidad=(id)=>{
   const index=carrito.value.findIndex(producto=>producto.id===id)
   if(carrito.value[index].cantidad<=1) return
   carrito.value[index].cantidad--
}

const incementarCantidad=(id)=>{
    const index=carrito.value.findIndex(producto=>producto.id===id)
    if(carrito.value[index].cantidad>=5) return
    carrito.value[index].cantidad++
}


const eliminarProducto=(id)=>{
    carrito.value=carrito.value.filter(producto=>producto.id!==id)
}

const vaciarCarrito=()=>{
    carrito.value=[]
}

//Agreagar al localstorage
const guardarLocaStorage=()=>{
    localStorage.setItem('carrito',JSON.stringify(carrito.value))
}
</script>

<template>
 <Header
 :carrito="carrito"
 :guitarra="guitarra"
  @decrementar-cantidad="decrementarCantidad"
  @incrementar-cantidad="incementarCantidad"
  @agregar-carrito="agreagarCarrito"
  @eliminar-producto="eliminarProducto"
  @vaciar-carrito="vaciarCarrito"
 />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitarra v-for="guitarra in guitarras"
            :guitarra="guitarra"
            @agreagar-carrito="agreagarCarrito"
           
            />
        </div>
    </main>

<Footer/>
    
</template>
