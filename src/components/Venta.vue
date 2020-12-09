<template>
    <v-layout align-start>
        <v-flex>
            <v-toolbar flat color="white">
                <v-toolbar-title>Ventas</v-toolbar-title>
                    <v-divider
                    class="mx-2"
                    inset
                    vertical
                    ></v-divider>
                    <v-spacer></v-spacer>
                    <v-text-field v-if="verNuevo==0" class="text-xs-center" v-model="search" append-icon="search" label="Búsqueda" single-line hide-details></v-text-field>
                    <v-btn v-if="verNuevo==0" @click="listar()" color="primary" dark class="mb-2">Buscar</v-btn>
                    <v-spacer></v-spacer>
                    <v-btn v-if="verNuevo==0" @click="mostrarNuevo" color="primary" dark class="mb-2">Nuevo</v-btn>
                    <v-dialog v-model="verArticulos" max-width="1000px">
                        <v-card>
                            <v-card-title>
                                <span class="headline">Seleccione un artículo</span>
                            </v-card-title>
                            <v-card-text>
                                <v-container grid-list-md>
                                    <v-layout wrap>
                                        <v-flex xs12 sm12 md12 lg12 xl12>
                                            <v-text-field append-icon="search" 
                                            class="text-xs-center" v-model="texto"
                                            label="Ingrese artículo a buscar" @keyup.enter="listarArticulo()">

                                            </v-text-field>
                                            <template>
                                               <v-data-table
                                                    :headers="cabeceraArticulos"
                                                    :items="articulos"
                                                    class="elevation-1"
                                                >
                                                    <template slot="items" slot-scope="props">
                                                        <td class="justify-center layout px-0">
                                                            <v-icon
                                                            small
                                                            class="mr-2"
                                                            @click="agregarDetalle(props.item)"
                                                            >
                                                            add
                                                            </v-icon>
                                                        </td>
                                                        <td>{{ props.item.nombre }}</td>
                                                        <td>{{props.item.categoria}}</td>
                                                        <td>{{props.item.descripcion}}</td>
                                                        <td>{{props.item.stock}}</td>
                                                        <td>{{props.item.precio_venta}}</td>
                                                    </template>
                                                    <template slot="no-data">
                                                        <h3>No hay artículos para mostrar.</h3>
                                                    </template>
                                                </v-data-table> 
                                            </template>
                                        </v-flex>
                                    </v-layout>
                                </v-container>
                            </v-card-text>
                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn @click="ocultarArticulos()" color="blue darken" flat>
                                    Cancelar
                                </v-btn>
                            </v-card-actions>
                        </v-card>
                    </v-dialog>
                    <v-dialog v-model="adModal" max-width="290px">
                        <v-card>
                            <v-card-title class="headline" v-if="adAccion==1">¿Activar Item?</v-card-title>
                            <v-card-title class="headline" v-if="adAccion==2">¿Anular Venta?</v-card-title>
                            <v-card-text>
                                Estás a punto de 
                                <span v-if="adAccion==1">Activar </span>
                                <span v-if="adAccion==2">Anular </span>
                                el ítem {{ adNombre }}
                            </v-card-text>
                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn color="green darken-1" flat="flat" @click="activarDesactivarCerrar">
                                    Cancelar
                                </v-btn>
                                <v-btn v-if="adAccion==1" color="orange darken-4" flat="flat" @click="activar">
                                    Activar
                                </v-btn>
                                <v-btn v-if="adAccion==2" color="orange darken-4" flat="flat" @click="desactivar">
                                    Anular
                                </v-btn>
                            </v-card-actions>

                        </v-card>
                    </v-dialog>
                    <v-dialog v-model="comprobanteModal" max-width="1000px">
                        <v-card>
                            <v-card-text>
                                <v-btn @click="crearPDF()"><v-icon>print</v-icon></v-btn>
                                <div id="factura">
                                    <header>
                                        <div id="logo">
                                        <img src="data:image/png;base64,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EAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQEAJCQAgIASEgBISAEBACQkAICAEhIASEgBAQAkJACAgBISAEhIAQGDkC/x/SAAxxkEh3pAAAAABJRU5ErkJggg==" id="imagen">
                                        </div>
                                        <div id="datos">
                                            <p id="encabezado">
                                                <b>Representaciones Magino Sac</b><br>Asia, Perú<br>Telefono:(+51)931742904<br>Email:Magino@gmail.com
                                            </p>
                                        </div>
                                        <div id="fact">
                                            <p>{{tipo_comprobante}}<br>
                                            {{serie_comprobante}}-{{num_comprobante}}<br>
                                            {{fecha_hora}}</p>
                                        </div>
                                    </header>
                                    <br>
                                    <section>
                                        <div>
                                            <table id="facliente">
                                                <tbody>
                                                    <tr>
                                                        <td id="cliente">
                                                            <strong>Sr(a). {{cliente}}</strong><br>
                                                            <strong>Documento:</strong> {{num_documento}}<br>
                                                            <strong>Dirección:</strong> {{direccion}}<br>
                                                            <strong>Teléfono:</strong> {{telefono}}<br>
                                                            <strong>Email:</strong> {{email}}
                                                        </td>
                                                    </tr>
                                                </tbody>
                                            </table>
                                        </div>
                                    </section>
                                    <br>
                                    <section>
                                        <div>
                                            <table id="facarticulo">
                                                <thead>
                                                    <tr id="fa">
                                                        <th>CANT</th>
                                                        <th>DESCRIPCION</th>
                                                        <th>PRECIO UNIT</th>
                                                        <th>DESC.</th>
                                                        <th>PRECIO TOTAL</th>
                                                    </tr>
                                                </thead>
                                                <tbody>
                                                    <tr v-for="det in detalles" :key="det.iddetalle_venta">
                                                        <td style="text-align:center;">{{det.cantidad}}</td>
                                                        <td>{{det.articulo}}</td>
                                                        <td style="text-align:right;">{{det.precio.toFixed(2)}}</td>
                                                        <td style="text-align:right;">{{det.descuento.toFixed(2)}}</td>
                                                        <td style="text-align:right;">{{(det.cantidad*det.precio-det.descuento).toFixed(2)}}</td>
                                                    </tr>
                                                </tbody>
                                                <tfoot>
                                                    <tr>
                                                        <th></th>
                                                        <th></th>
                                                        <th></th>
                                                        <th style="text-align:right;">SUBTOTAL</th>
                                                        <th style="text-align:right;">S./ {{totalParcial=(total-totalImpuesto).toFixed(2)}}</th>
                                                    </tr>
                                                    <tr>
                                                        <th></th>
                                                        <th></th>
                                                        <th></th>
                                                        <th style="text-align:right;">IGV ({{impuesto}}%)</th>
                                                        <th style="text-align:right;">S./ {{totalImpuesto=((total*impuesto)/(100+impuesto)).toFixed(2)}}</th>
                                                    </tr>
                                                    <tr>
                                                        <th></th>
                                                        <th></th>
                                                        <th></th>
                                                        <th style="text-align:right;">TOTAL</th>
                                                        <th style="text-align:right;">S./ {{total=(calcularTotal).toFixed(2)}}</th>
                                                    </tr>
                                                </tfoot>
                                            </table>
                                        </div>
                                    </section>
                                    <br>
                                    <br>
                                    <footer>
                                        <div id="gracias">
                                            <p><b>Gracias por su compra!</b></p>
                                        </div>
                                    </footer>
                                </div>
                                <v-btn @click="ocultarComprobante()" color="blue darken-1" flat>Cancelar</v-btn>
                            </v-card-text>
                        </v-card>
                    </v-dialog>
                </v-toolbar>
            <v-data-table
                :headers="headers"
                :items="ventas"
                :search="search"
                class="elevation-1"
                v-if="verNuevo==0"
            >
                <template slot="items" slot-scope="props">
                    <td class="justify-center layout px-0">
                        <v-icon
                        small
                        class="mr-2"
                        @click="verDetalles(props.item)"
                        >
                        tab
                        </v-icon>
                        <v-icon
                        small
                        class="mr-2"
                        @click="mostrarComprobante(props.item)"
                        >
                        print
                        </v-icon>
                        <template v-if="props.item.estado=='Aceptado'">
                            <v-icon
                            small
                            @click="activarDesactivarMostrar(2,props.item)"
                            >
                            block
                            </v-icon>
                        </template>
                    </td>
                    <td>{{ props.item.usuario }}</td>
                    <td>{{ props.item.cliente}}</td>
                    <td>{{ props.item.tipo_comprobante }}</td>
                    <td>{{ props.item.serie_comprobante }}</td>
                    <td>{{ props.item.num_comprobante }}</td>
                    <td>{{ props.item.fecha_hora }}</td>
                    <td>{{ props.item.impuesto }}</td>
                    <td>{{ props.item.total }}</td>
                    <td>
                        <div v-if="props.item.estado=='Aceptado'">
                            <span class="blue--text">Aceptado</span>
                        </div>
                        <div v-else>
                            <span class="red--text">{{props.item.estado}}</span>
                        </div>
                    </td>
                </template>
                <template slot="no-data">
                <v-btn color="primary" @click="listar">Resetear</v-btn>
                </template>
            </v-data-table>
            <v-container grid-list-sm class="pa-4 white" v-if="verNuevo">
                <v-layout row wrap>
                    <v-flex xs12 sm4 md4 lg4 xl4>
                        <v-select v-model="tipo_comprobante" 
                        :items="comprobantes" label="Tipo Comprobante">
                        </v-select>
                    </v-flex>
                    <v-flex xs12 sm4 md4 lg4 xl4>
                        <v-text-field v-model="serie_comprobante" label="Serie Comprobante">
                        </v-text-field>
                    </v-flex>
                    <v-flex xs12 sm4 md4 lg4 xl4>
                        <v-text-field v-model="num_comprobante" label="Número Comprobante">
                        </v-text-field>
                    </v-flex>
                    <v-flex xs12 sm8 md8 lg8 xl8>
                        <v-select v-model="idcliente"
                        :items="clientes" label="Cliente">
                        </v-select>
                    </v-flex>
                    <v-flex xs12 sm4 md4 lg4 xl4>
                        <v-text-field type="number" v-model="impuesto" label="Impuesto">
                        </v-text-field>
                    </v-flex>
                    <v-flex xs12 sm8 md8 lg8 xl8>
                        <v-text-field @keyup.enter="buscarCodigo()" v-model="codigo" label="Código">
                        </v-text-field>
                    </v-flex>
                    <v-flex xs12 sm2 md2 lg2 xl2>
                        <v-btn @click="mostrarArticulos()" small fab dark color="teal">
                            <v-icon dark>list</v-icon>
                        </v-btn>
                    </v-flex>
                    <v-flex xs12 sm2 md2 lg2 xl2 v-if="errorArticulo">
                        <div class="red--text" v-text="errorArticulo">
                        </div>
                    </v-flex>
                    <v-flex xs12 sm12 md12 lg12 xl12>
                        <v-data-table
                            :headers="cabeceraDetalles"
                            :items="detalles"
                            hide-actions
                            class="elevation-1"
                        >
                            <template slot="items" slot-scope="props">
                                <td class="justify-center layout px-0">
                                    <v-icon
                                    small
                                    class="mr-2"
                                    @click="eliminarDetalle(detalles,props.item)"
                                    >
                                    delete
                                    </v-icon>
                                </td>
                                <td>{{ props.item.articulo }}</td>
                                <td><v-text-field type="number" v-model="props.item.cantidad"></v-text-field></td>
                                <td><v-text-field type="number" v-model="props.item.precio"></v-text-field></td>
                                <td><v-text-field type="number" v-model="props.item.descuento"></v-text-field></td>
                                <td>S./ {{ props.item.cantidad * props.item.precio - props.item.descuento}}</td>
                            </template>
                            <template slot="no-data">
                                <h3>No hay artículos agregados al detalle.</h3>
                            </template>
                        </v-data-table>
                        <v-flex class="text-xs-right">
                            <strong>Total Parcial: </strong>S./ {{totalParcial=(total-totalImpuesto).toFixed(2)}}
                        </v-flex>
                        <v-flex class="text-xs-right">
                            <strong>Total Impuesto: </strong>S./ {{totalImpuesto=((total*impuesto)/(100+impuesto)).toFixed(2)}}
                        </v-flex>
                        <v-flex class="text-xs-right">
                            <strong>Total Neto: </strong>S./ {{total=(calcularTotal).toFixed(2)}}
                        </v-flex>
                    </v-flex>
                    <v-flex xs12 sm12 md12 lg12 xl12>
                        <div class="red--text" v-for="v in validaMensaje" :key="v" v-text="v">
                        </div>
                    </v-flex>
                    <v-flex xs12 sm12 md12 lg12 xl12>
                        <v-btn @click="ocultarNuevo()" color="blue darken-1" flat>Cancelar</v-btn>
                        <v-btn v-if="verDet==0" @click="guardar()" color="success">Guardar</v-btn>
                    </v-flex>
		        </v-layout>
            </v-container>
        </v-flex>
    </v-layout>
</template>
<script>
    import axios from 'axios'
    import jsPDF from 'jspdf'
    import html2canvas from 'html2canvas';
    export default {
        data(){
            return {
                ventas:[],                
                dialog: false,
                headers: [
                    { text: 'Opciones', value: 'opciones', sortable: false },
                    { text: 'Usuario', value: 'usuario' },
                    { text: 'Cliente', value: 'cliente' },
                    { text: 'Tipo Comprobante', value: 'tipo_comprobante' },
                    { text: 'Serie Comprobante', value: 'serie_comprobante', sortable: false  },
                    { text: 'Número Comprobante', value: 'num_comprobante', sortable: false  },
                    { text: 'Fecha', value: 'fecha_hora', sortable: false  },
                    { text: 'Impuesto', value: 'impuesto', sortable: false  },
                    { text: 'Total', value: 'total', sortable: false  },
                    { text: 'Estado', value: 'estado', sortable: false  }                
                ],
                cabeceraDetalles: [
                    { text: 'Borrar', value: 'borrar', sortable: false },
                    { text: 'Artículo', value: 'articulo', sortable: false },
                    { text: 'Cantidad', value: 'cantidad', sortable: false  },
                    { text: 'Precio', value: 'precio', sortable: false  },
                    { text: 'Descuento', value: 'descuento', sortable: false  },
                    { text: 'Subtotal', value: 'subtotal', sortable: false  }                
                ],
                detalles:[                    
                ],
                search: '',
                id: '',
                idcliente:'',
                clientes:[                   
                ],
                tipo_comprobante: '',
                comprobantes: ['FACTURA','BOLETA','TICKET','GUIA'],
                serie_comprobante: '',
                num_comprobante: '',
                impuesto: 18,
                codigo:'',
                verNuevo:0,
                errorArticulo:null,
                totalParcial:0,
                totalImpuesto:0,
                total:0,
                cabeceraArticulos: [
                    {text: 'Seleccionar', value: 'seleccionar', sortable: false },
                    { text: 'Artículo', value: 'articulo'},
                    { text: 'Categoría', value: 'categoria' },
                    { text: 'Descripción', value: 'descripcion', sortable: false },
                    { text: 'Stock', value: 'stock', sortable: false  },
                    { text: 'Precio Venta', value: 'precio_venta', sortable: false  }            
                ],
                articulos:[],
                texto:'',
                verArticulos:0,
                verDet: 0,
                valida: 0,
                validaMensaje:[],
                adModal: 0,
                adAccion: 0,
                adNombre: '',
                adId: '',
                comprobanteModal:0,
                cliente:'',
                fecha_hora:'',
                num_documento:'',
                direccion:'',
                telefono:'',
                email:''             
            }
        },
        computed: {
            calcularTotal:function(){
                var resultado=0.0;
                for(var i=0;i<this.detalles.length;i++){
                    resultado=resultado+(this.detalles[i].precio*this.detalles[i].cantidad-this.detalles[i].descuento);
                }
                return resultado;
            }
        },

        watch: {
            dialog (val) {
            val || this.close()
            }
        },

        created () {
            this.listar();
            this.select();
        },
        methods:{
            crearPDF(){
                var quotes = document.getElementById('factura');
                html2canvas(quotes).then(function (canvas) {
                    var imgData = canvas.toDataURL('image/png');
                    var imgWidth = 210;
                    var pageHeight = 295;
                    var imgHeight = canvas.height * imgWidth / canvas.width;
                    var heightLeft = imgHeight;
                    var doc = new jsPDF();
                    var position = 0;

                    doc.addImage(imgData, 'PNG', 0, position, imgWidth, imgHeight);                    
                    doc.save('ComprobanteVenta.pdf');
                });
            },
            mostrarComprobante(item){
                this.limpiar();
                this.tipo_comprobante=item.tipo_comprobante;
                this.serie_comprobante=item.serie_comprobante;
                this.num_comprobante=item.num_comprobante;
                this.cliente=item.cliente;
                this.num_documento=item.num_documento;
                this.direccion=item.direccion;
                this.telefono=item.telefono;
                this.email=item.email;
                this.fecha_hora=item.fecha_hora;
                this.impuesto=item.impuesto;
                this.listarDetalles(item.idventa);
                this.comprobanteModal=1;
            },
            ocultarComprobante(){
                this.comprobanteModal=0;
                this.limpiar();
            },
            mostrarNuevo(){
                this.verNuevo=1;
            },
            ocultarNuevo(){
                this.verNuevo=0;
                this.limpiar();
            },
            buscarCodigo(){
                let me=this;
                me.errorArticulo=null;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.get('api/Articulos/BuscarCodigoVenta/'+this.codigo,configuracion)
                .then(function(response){
                    //console.log(response);
                    me.agregarDetalle(response.data);
                }).catch(function(error){
                    console.log(error);
                    me.errorArticulo='No existe el artículo';
                });
            },
            listarArticulo(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.get('api/Articulos/ListarVenta/'+me.texto,configuracion).then(function(response){
                    //console.log(response);
                    me.articulos=response.data;
                }).catch(function(error){
                    console.log(error);
                });
            },
            mostrarArticulos(){
                this.verArticulos=1;
            },
            ocultarArticulos(){
                this.verArticulos=0;
            },
            agregarDetalle(data = []){
                this.errorArticulo=null;
                if (this.encuentra(data['idarticulo'])){
                    this.errorArticulo="El artículo ya ha sido agregado."
                }
                else{
                    this.detalles.push(
                        {idarticulo:data['idarticulo'],
                        articulo: data['nombre'],
                        cantidad:1,
                        precio:data['precio_venta'],
                        descuento:0}
                    );
                }
                
            },
            encuentra(id){
                var sw=0;
                for(var i=0;i<this.detalles.length;i++){
                    if (this.detalles[i].idarticulo==id){
                        sw=1;
                    }
                }
                return sw;
            },
            eliminarDetalle(arr,item){
                var i= arr.indexOf(item);
                if (i!==-1){
                    arr.splice(i,1);
                }
            },
            listar(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                let url='';
                if (!me.search){
                    url='api/Ventas/Listar';
                }
                else{
                    url='api/Ventas/ListarFiltro/'+me.search;
                }
                axios.get(url,configuracion).then(function(response){
                    //console.log(response);
                    me.ventas=response.data;
                }).catch(function(error){
                    console.log(error);
                });
            },
            listarDetalles(id){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.get('api/Ventas/ListarDetalles/'+id,configuracion).then(function(response){
                    //console.log(response);
                    me.detalles=response.data;
                }).catch(function(error){
                    console.log(error);
                });
            },
            verDetalles(item){
                this.limpiar();
                this.tipo_comprobante=item.tipo_comprobante;
                this.serie_comprobante=item.serie_comprobante;
                this.num_comprobante=item.num_comprobante;
                this.idcliente=item.idcliente;
                this.impuesto=item.impuesto;
                this.listarDetalles(item.idventa);
                this.verNuevo=1;
                this.verDet=1;
            },
            select(){
                let me=this;
                var clientesArray=[];
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.get('api/Personas/SelectClientes',configuracion).then(function(response){
                    clientesArray=response.data;
                    clientesArray.map(function(x){
                        me.clientes.push({text: x.nombre,value:x.idpersona});
                    });
                }).catch(function(error){
                    console.log(error);
                });
            },
            limpiar(){
                this.id="";
                this.idcliente="";
                this.tipo_comprobante="";
                this.serie_comprobante="";
                this.num_comprobante="";
                this.impuesto="18";
                this.codigo="";
                this.detalles=[];
                this.total=0;
                this.totalImpuesto=0;
                this.totalParcial=0;
                this.verDet=0;
            },
            guardar () {
                if (this.validar()){
                    return;
                }
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};                
                let me=this;
                axios.post('api/Ventas/Crear',{
                    'idcliente':me.idcliente,
                    'idusuario':me.$store.state.usuario.idusuario,
                    'tipo_comprobante': me.tipo_comprobante,
                    'serie_comprobante': me.serie_comprobante,
                    'num_comprobante':me.num_comprobante,
                    'impuesto': me.impuesto,
                    'total':me.total,
                    'detalles':me.detalles
                },configuracion).then(function(response){
                    me.ocultarNuevo();
                    me.listar();
                    me.limpiar();                        
                }).catch(function(error){
                    console.log(error);
                });
            },
            validar(){
                this.valida=0;
                this.validaMensaje=[];

                if (!this.idcliente){
                    this.validaMensaje.push("Seleccione un cliente.");
                }
                if (!this.tipo_comprobante){
                    this.validaMensaje.push("Seleccione un tipo de comprobante.");
                }
                if (!this.num_comprobante){
                    this.validaMensaje.push("Ingrese el número del comprobante.");
                }
                if (!this.impuesto || this.impuesto<0){
                    this.validaMensaje.push("Ingrese un impuesto válido.");
                }
                if (this.detalles.length<=0){
                    this.validaMensaje.push("Ingrese al menos un artículo al detalle.");
                }
                if (this.validaMensaje.length){
                    this.valida=1;
                }
                return this.valida;
            },
            activarDesactivarMostrar(accion,item){
                this.adModal=1;
                this.adNombre=item.num_comprobante;
                this.adId=item.idventa;                
                if (accion==1){
                    this.adAccion=1;
                }
                else if (accion==2){
                    this.adAccion=2;
                }
                else{
                    this.adModal=0;
                }
            },
            activarDesactivarCerrar(){
                this.adModal=0;
            },
            /*
            activar(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.put('api/Usuarios/Activar/'+this.adId,{},configuracion).then(function(response){
                    me.adModal=0;
                    me.adAccion=0;
                    me.adNombre="";
                    me.adId="";
                    me.listar();                       
                }).catch(function(error){
                    console.log(error);
                });
            },
            */
            desactivar(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.put('api/Ventas/Anular/'+this.adId,{},configuracion).then(function(response){
                    me.adModal=0;
                    me.adAccion=0;
                    me.adNombre="";
                    me.adId="";
                    me.listar();                       
                }).catch(function(error){
                    console.log(error);
                });
            }
        }        
    }
</script>
<style>
    #factura {
        padding: 20px;
        font-family: Arial, sans-serif;
        font-size: 16px ;
    }

    #logo {
        float: left;
        margin-left: 2%;
        margin-right: 2%;
    }
    #imagen {
        width: 100px;
    }

    #fact {
        font-size: 18px;
        font-weight: bold;
        text-align: center;
    }   

    #datos {
        float: left;
        margin-top: 0%;
        margin-left: 2%;
        margin-right: 2%;
        /*text-align: justify;*/
    }

    #encabezado {
        text-align: center;
        margin-left: 10px;
        margin-right: 10px;
        font-size: 16px;
    }

    section {
        clear: left;
    }

    #cliente {
        text-align: left;
    }

    #facliente {
        width: 40%;
        border-collapse: collapse;
        border-spacing: 0;
        margin-bottom: 15px;
    }

    #fa {
        color: #FFFFFF;
        font-size: 14px;
    }

    #facarticulo {
        width: 100%;
        border-collapse: collapse;
        border-spacing: 0;
        padding: 20px;
        margin-bottom: 15px;
    }

    #facarticulo thead {
        padding: 20px;
        background: #2183E3;
        text-align: center;
        border-bottom: 1px solid #FFFFFF;
    }

    #gracias {
        text-align: center;
    }
</style>
