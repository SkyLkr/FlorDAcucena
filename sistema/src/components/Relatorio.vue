<template>
    <b-container id="contRel" v-if="!loading">
        <div class="row justify-content-end">
            <router-link id="linkGeraRel" to="/relatorio/dados" class="botao">⚙️</router-link> 
        </div>
        <br>
        <div id="tabelaRel" class="row justify-content-between">
            <table class="table">
                <thead>
                    <th scope="col">Custos Fixos</th>
                    <th scope="col">Custos Variáveis</th>
                    <th scope="col">Total</th>
                    <th scope="col">Entrada</th>
                    <th scope="col">Lucro</th>
                </thead>
                <tbody>
                    <tr>
                        <td id="custosFixos">{{ items.fixos.toFixed(2) }}</td>
                        <td>R$ {{ items.variaveis.toFixed(2) }}</td>
                        <td>R$ {{ items.total.toFixed(2) }}</td>
                        <td>R$ {{ items.entrada.toFixed(2) }}</td>
                        <td :class="(items.lucro < 0) ? ('text-danger') : ('') ">R$ {{ items.lucro.toFixed(2) }}</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </b-container>
</template>


<script>
import { pedidosRef, db } from '../firebase'
import { isArray } from 'util';

export default {
    name: 'ListarPedidos',
    data(){
        return{
            fields: [
                { key: 'fixos', label: 'Custos Fixos' },
                { key: 'variaveis', label: 'Custos Variáveis' },
                { key:'total', label: 'Total'},
                { key: 'entrada', label: 'Entrada' },
                { key:'lucro', label: 'Lucro'}
            ],
            items: {
                fixos: 0.0,
                variaveis: 0.0,
                total: 0.0,
                entrada: 0.0,
                lucro: 0.0
            },
            loading: true
        }
    },
    methods: {
        loadFirebase(callback) {
            var items = {
                fixos: 0.0,
                variaveis: 0.0,
                total: 0.0,
                entrada: 0.0,
                lucro: 0.0
            }

            db.ref('gastos').once('value', function(snapshot) {
                var v = snapshot.val();
                items.fixos = parseFloat(v.agua) + parseFloat(v.aluguel) + parseFloat(v.internet) + parseFloat(v.luz);

                pedidosRef.once('value', function(snapshot) {
                    snapshot.forEach(function(pedidoSnap) {
                        var ped = pedidoSnap.val();
                        pedidosRef.child(pedidoSnap.key + '/materiais').once('value', function(materialSnap) {
                            materialSnap.forEach(material => {
                                var mat = material.val()
                                console.log(mat)
                                items.variaveis += parseFloat(mat.preco)
                            });
                            callback(items);
                        });
                    });
                });
            });
        },
        updateCalculo(items) {

            items.total = items.variaveis + items.fixos;
            items.entrada = items.variaveis * 1.2;

            items.lucro = items.entrada - items.variaveis;

            this.items = items;

            this.loading = false;
        }
    },
    mounted() {
        this.loadFirebase(this.updateCalculo);
    }
}
</script>

<style>

#tabelaRel {
    padding-left: 2px;
    padding-right: 2px;
    font-weight: bold;
}

.table .thead-light th {
    color: white;
    background-color: #d9779a;
    border: 2px solid #c79bbf;
    font-size: 1.25rem;
}

.table-bordered th, .table-bordered td {
    border: 2px solid #c79bbf;
}


</style>
