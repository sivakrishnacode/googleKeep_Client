<template>
  <div class="row justify-center ">
     <div class="q-gutter-y-md q-pt-md">
         <div class="row ">
           <q-input outlined  v-model="itemInput" label="Filled" />
            <q-btn rounded color="primary" icon="add" @click="() => submit(itemInput)"/>
        </div>
        <div v-if="toDoItems.length !== 0" class="row justify-center">
           <q-btn rounded color="primary" label="Clear All" @click="() => remove('*')"/>
        </div>
  
        <q-list class="q-gutter-y-sm">
        <q-item class="bg-info" clickable v-ripple :key="item.id" v-for="item in toDoItems" >
  
          <q-item-section class="row no-wrap">
            <q-item-label>{{  item.data }}</q-item-label>
            <q-item-label caption> {{ item.id }}</q-item-label>
          </q-item-section>
  
        <q-item-section avator>
           <q-btn rounded  color="primary" icon="cancel" @click="remove({id:item.id})"/>
        </q-item-section>
  
        </q-item>
        </q-list>
     </div>
  </div>
  </template>
  
  <script>
  import { defineComponent, onMounted } from 'vue'
  import { ref } from 'vue'
  import { api } from 'src/boot/axios.js'
  import { useQuasar } from 'quasar'
  
  export default defineComponent({
    name: 'IndexPage',
    setup(){
  
     const $q = useQuasar()
  
     const toDoItems = ref([])
     const itemInput = ref('')
  
     async function submit(data){
  
        if(data !== ''){
           await api({
           method: "POST",
           url: "/addItem",
           params: {
              data: data
           }
            }).then((res) => {
              $q.notify({
              message: 'Data Added succesfully',
              position: 'top-right',
              type: 'positive'
           })
           }).catch((err) => {
           console.log(err);
              })
        }
     
        itemInput.value = ''
        getToDoItemsList()
     }
  
     async  function remove(data = {id : '*'}){
  
      await api({
           method: "POST",
           url: "/removeItem",
           params: data
        }).then((res) => {
           $q.notify({
              message: 'Data removed succesfully',
              position: 'top-right',
              type: 'positive'
           })
        }).catch((err) => {
           console.log(err);
        })
  
       getToDoItemsList()
     }
  
       async function getToDoItemsList(){
        toDoItems.value = []
  
       await api({
           method: "GET",
           url: "/getItems"
        }).then((res) => {
  
           res.data.map((m) => {
              toDoItems.value.push(m)
           })
        
        }).catch((err) => {
           console.log(err);
        })
  
        //console.log(toDoItems.value);
     }
  
  
     onMounted(() => {
        getToDoItemsList()
     })
  
  
     return{
        submit,
        itemInput,
        toDoItems,
        remove
     }
    }
  })
  </script>