<template>
<div class="container">
    <h1>Empleados</h1>
    <b-alert
    :show="dismissCountDown"
    dismissible
    :variant="mensaje.color"
    @dismissed="dismissCountDown=0"
    @dismiss-count-down="countDownChanged"
>
    {{mensaje.texto}}
    
</b-alert>

<!-- @submit.prevent para que no se actualice-->
<form @submit="editarEmpleado(empleadoEditar)" v-if="editar">
    <h3>Editar Empleado</h3>
    <input type="text" class="form-control my-2" placeholder="Nombre" v-model="empleadoEditar.name">
    <input type="text" class="form-control my-2" placeholder="Salario" v-model="empleadoEditar.salary">
    <b-button class="btn-warning my-2 mx-2" type="submit">Editar</b-button>
    <b-button class="btn-dark my-2" type="submit" @click="editar = false">Cancelar</b-button>
  </form>
<form @submit="agregarEmpleado()" v-if="!editar">
    <h3>Agregar Empleado</h3>
    <input type="text" class="form-control my-2" placeholder="Nombre" v-model="empleado.name">
    <input type="text" class="form-control my-2" placeholder="Salario" v-model="empleado.salary">
    <b-button class="btn-success my-2 btn-block" type="submit">Agregar</b-button>
  </form>




  <table class="table">
  <thead>
    <tr>
      <th scope="col">#</th>
      <th scope="col">Nombre</th>
      <th scope="col">Salario</th>
      <th scope="col">Acciones</th>
      
    </tr>
  </thead>
  <tbody>
    <tr v-for="(item,index) in empleados" :key="index">
      <th scope="row">{{item.id}}</th>
      <td>{{item.name}}</td>
      <td>{{item.salary}}</td>
      <td>
        <!---<b-button @click="alerta()">Accion</b-button>-->
        <b-button @click="eliminarEmpleado(item.id)" class="btn-danger btn-sm mx-2">Eliminar</b-button>
        <b-button @click="activarEdicion(item.id)" class="btn-warning btn-sm">Editar</b-button>
        </td>
    </tr>
  </tbody>
</table>
</div>
</template>

<script>
export default {
    data(){
        return{
            empleados: [],
            dismissSecs: 5,
            dismissCountDown: 0,
            mensaje: {color: '', texto: ''},
            empleado:{id:0,name:'',salary:''},
            editar: false,
            empleadoEditar: {}
        }
    },
    created(){
        this.listarEmpleados();
    },
    methods: {
        alerta(){
          this.mensaje.color="success"
          this.mensaje.texto="Probando alerta"
          this.showAlert();
        },
        listarEmpleados(){
            this.axios.get('/')
            .then(res =>{
                console.log(res.data);
                this.empleados = res.data;
            })
            .catch(err=>{
                console.log(err.response);
            })
        },
        agregarEmpleado(){
          this.axios.post('/',this.empleado)
          .then(res=>{
            this.empleados.push(res.data)
            this.empleado.name=''
            this.empleado.salary=''

            this.mensaje.color='success'
            this.mensaje.texto='Empleado Agregado'
            this.showAlert();
          })
          .catch(err=>{
            console.log(err.response);
            this.mensaje.color='danger'
            this.mensaje.color='fallo'
            this.showAlert();
          })
        },
        eliminarEmpleado(id){

          this.axios.delete(`/${id}`)
          .then(res=>{
            const index=this.empleados.findIndex(item=>item.id===res.data.id);
            this.empleados.splice(index, 1);
            this.mensaje.color='success'
            this.mensaje.texto='Empleado Eliminado'
            this.showAlert();
          })
          .catch(err=>{
            console.log(err.response);
          })

        },
        activarEdicion(id){
          this.editar=true;
          this.axios.get(`/${id}`)
          .then(res=>{
            this.empleadoEditar=res.data;
          })
          .catch(err=>{
            console.log(err.response);
          })
        },
        editarEmpleado(item){
          this.axios.put(`${item.id}`,item)
          .then(res=>{
            const index = this.empleados.findIndex(em=>em.id===res.data.id);
            this.empleados[index].name=res.data.name
            this.empleados[index].salary=res.data.salary
            this.mensaje.color='success'
            this.mensaje.texto='Empleado Editado'
            this.showAlert();
            this.editar=false
          })
          .catch(err=>{
            console.log(err.response);
          })
        },
        countDownChanged(dismissCountDown) {
          this.dismissCountDown = dismissCountDown
        },
        showAlert() {
          this.dismissCountDown = this.dismissSecs
        }
  }
    
}
</script>