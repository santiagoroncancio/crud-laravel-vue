<template>
    <div class="container">
        <h1 class="text-center">Gestionar Articulos</h1>
        <hr>

        <button type="button" class="btn btn-success mb-3" @click="modificar=false;openModal();">
            Agregar
        </button>

        <table class="table table-striped">
            <thead>
                <tr>
                    <th scope="col">#</th>
                    <th scope="col">Nombre</th>
                    <th scope="col">Descripción</th>
                    <th scope="col">Stock</th>
                    <th scope="col" colspan="2" class="text-center">Acción</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item of articulos" :key="item.id">
                    <th scope="row">{{item.id}}</th>
                    <td>{{item.name}}</td>
                    <td>{{item.desc}}</td>
                    <td>{{item.stock}}</td>
                    <td><button class="btn btn-warning" @click="modificar=true;openModal(item);">Editar</button></td>
                    <td><button class="btn btn-danger" @click="eliminar(item.id)">Eliminar</button></td>
                </tr>
            </tbody>
        </table>

        <!-- Modal -->
        <div class="modal" :class="{mostrar:modal}">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">{{titleModal}}</h5>
                        <button type="button" class="close" @click="closeModal()">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <div class="form">
                              <div class="form-group">
                                <label for="name">Nombre</label>
                                <input type="text" id="name" class="form-control" v-model="articulo.name">
                            </div>
                              <div class="form-group">
                                <label for="desc">Descripción</label>
                                <textarea id="desc" cols="30" rows="10" class="form-control" v-model="articulo.desc"></textarea>
                            </div>
                              <div class="form-group">
                                <label for="stock">Stock</label>
                                <input type="number" id="stock" class="form-control" v-model="articulo.stock" min="0">
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" @click="closeModal();">Cancelar</button>
                        <button type="button" class="btn btn-success" @click="guardar();">Guardar</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                titleModal:'',
                articulos: [],
                modal:0,
                id:0,
                articulo:{
                    name:'',
                    desc:'',
                    stock:1,
                },
                modificar:false,
            }
        },
        methods: {
            async listar() {
                const res = await axios.get('articulos');
                this.articulos = res.data;
            },
            async eliminar(id) {
                const res = await axios.delete('/articulos/' + id);
                this.listar();
            },
            async guardar(){
                try {
                    if(!this.modificar){
                       const res = await axios.post('/articulos',this.articulo); 
                    }else{
                        const res = await axios.put('/articulos/'+this.id, this.articulo);      
                    }
                } catch (error) {
                    console.log(error);
                }
                this.closeModal();
                this.listar();
            },
            openModal(art={}){
                if(! this.modificar){
                    this.titleModal="Crear articulos";
                    this.articulo.name='';
                    this.articulo.desc='';
                    this.articulo.stock=0;
                    this.id = 0;
                }else{
                    this.titleModal="Modificar articulos";
                    this.articulo.name=art.name;
                    this.articulo.desc=art.desc;
                    this.articulo.stock=art.stock;
                    this.id = art.id;
                }
                this.modal=1;
            },
            closeModal(){
                this.modal=0;
            }
        },
        created() {
            this.listar();
        }
    }
</script>
<style>
.mostrar{
    display: list-item;
    opacity: 1;
    /* background-color: rgba(0, 0, 0, 0.363); */
}
</style>