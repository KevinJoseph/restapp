<template>
<div class="container">
        <!-- Formulario para añadir pacientes -->
        <section class="form">
              <form @submit.prevent="editInvestment(investment)" v-if="modoEditar">
                <h3>Editar Inversión</h3>
                <div class="form-group">
                <label for="example-date-input">Fecha</label>
                
                    <input class="form-control" type="date" id="example-date-input" v-model="investment.date">
              
                </div>
               <div class="form-group">

                    <input type="text" class="form-control mb-2" placeholder="00.00" v-model="investment.total">

                </div>
                <button class="btn btn-success" type="submit">Guardar</button>
                <button class="btn btn-default" type="submit" 
                    @click="cancelarEdicion">Cancelar</button>
                </form>
                <form @submit.prevent="addInvestment" v-else>
                
                <div class="form-group">
                <label for="example-date-input">Fecha</label>
                    <input class="form-control" type="date" id="example-date-input" v-model="investment.date">
                </div>
                <div class="form-group">
                    <label for="Total">Total</label>
                    
                    <input type="text" class="form-control mb-2" placeholder="00.00" v-model="investment.total">
                
                </div>
                <button class="btn btn-primary" type="submit">Agregar</button>
                </form>
        </section>
        <!-- Tabla donde se muestran los datos -->
      
        <section class="data">
            <br>
            <table class="table table-responsive">
                <thead>
                    <tr>
                        <th scope="col">#</th>
                        <th scope="col">Fecha</th>
                        <th scope="col">Total</th>
                        <th scope="col">Acciones</th>         
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(investment, index) in investments" v-bind:key="index">
                        <td>{{index+1 }}</td>
                        <td>{{investment.date}}</td>
                        <td>{{investment.total}}</td>
                        <td>
                                <!-- Botón para mostrar el formulario de actualizar -->
                                <button @click="editarFormulario(investment)" class="btn btn-warning"> <i class="fa fa-pencil" aria-hidden="true"></i> </button>
                                <!-- Botón para borrar -->
                                <button @click="deleteInvestment(investment,index)" class="btn btn-danger"><i class="fa fa-trash" aria-hidden="true"></i>  </button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </section>
    </div>
</template>

<script>
    export default {
        mounted() {
               axios.get('investments').then((response) => {
                this.investments = response.data;
                });
                this.fechaActual();
        },
        data() {
            return{
                investments: [] ,
                investment:{date:'',total:''},
                modoEditar:false
            }
        },
        methods:{
            addInvestment: function() {
                const params = this.investment; 
                 console.log('params: '+this.params);
                axios.post('investments', params)
                .then((response) => {
                this.investments.push(response.data);
                console.log(this.investments);
                });    
                this.investment = {date:'',total:''};      
            },
            editarFormulario(investment){
                this.investment.id = investment.id;
                this.investment.date = investment.date;
                this.investment.total = investment.total;
                this.modoEditar = true;
            },
            editInvestment: function (investment) {
                const params = {date: investment.date, total: investment.total};
                console.log('params: '+params);
                axios.put(`investments/${investment.id}`, params)
                    .then(response => {
                         this.modoEditar = false;
                         const index = this.investments.findIndex(item => item.id === investment.id);
                         this.investments[index] = response.data;
                            axios.get('investments')
                            .then( response => {
                               this.investments = response.data;     
                            });
                    });
            },
            deleteInvestment: function (investment, index) {
                const confirmation = confirm(`Se eliminara el inversión: ${investment.total}`);
                if(confirmation){
                    axios.delete(`investments/${investment.id}`)
                    .then(()=>{
                        this.investments.splice(index,1)
                    });
                }
            },
            cancelarEdicion: function () {
                this.modoEditar = false;
                this.investment = {total:''};
            },
            fechaActual: function() {
                var dateFormat = require('dateformat');
                var now = new Date();
                this.investment.date = dateFormat(now, "yyyy-mm-dd");
            },
        }
    }
</script>