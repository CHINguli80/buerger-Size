<template>
    <div id="burger-table">
        <Message :msg="msg" v-show="msg" />
        <div>
            <div id="burger-table-heading">
                <div class="order-id">#:</div>
                <div>Cliente:</div>
                <div>Pão:</div>
                <div>Carne:</div>
                <div>Opcionais:</div>
                <div>Bebida:</div>
                <div>Acompanhante:</div>
                <div>Ações:</div>
            </div>
            <div id="burger-table-rows">
                <div class="burger-table-row" v-for="dado in dados" :key="dado.id">
                    <div class="order-number">{{dado.id}}</div>
                    <div>{{dado.nome}}</div>
                    <div>{{dado.pao}}</div>
                    <div>{{dado.carne}}</div>
                    <div>
                        <ul>
                            <li v-for="(opcional, index) in dado.opcionais" :key="index">{{opcional}}</li>
                        </ul>
                        </div>
                    <div>{{dado.bebida}}</div>
                    <div>{{dado.acompanhante}}</div>
                    <div id="lado">
                        <select name="status" class="status" @change="updateBurger($event, dado.id)">
                            <option value="">Seleciona</option>
                            <option v-for="statu in status" :key="statu.id" :value="statu.tipo" 
                            :selected="dado.status == statu.tipo">
                            {{statu.tipo}}
                        </option>
                        </select>
                        <button class="delete-btn" @click="deleteBurger(dado.id)">Cancelar</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import Message from './Message.vue'

export default {
    name: "Dashbord",
    data() {
        return {
           dados: null,
           dados_Id: null,
           status: [],
           msg: null
        }
    },
    methods: {
        async getPedidos() {

            const req = await fetch("http://localhost:3000/burgers");
            const data = await req.json()
            
            this.dados = data
           
            
            this.getStatus()
        },
        async getStatus() {
            
            const req = await fetch("http://localhost:3000/status");
            const data = await req.json()

            this.status = data

        },
        async deleteBurger(id) {

            const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: "DELETE"
            })

            const res = await req.json()

            this.getPedidos()
            this.msg = `O pedido Nº ${id} foi cancelado`
            setTimeout(() => this.msg = "", 3000)

        },
        async updateBurger(event, id) {

            const option = event.target.value
            const dataJson = JSON.stringify({ status: option })

            const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: "PATCH",
                headers: { "Content-Type": "application/json"},
                body: dataJson
            })

            const res = await req.json() 

            this.msg = `O pedido Nº ${res.id} foi atualizado para ${res.status}`
            setTimeout(() => this.msg = "", 3000)
        } 
    },
    mounted() {
        this.getPedidos()
    },
    components: {
        Message
    }
}
</script>

<style scoped>
    #burger-table {
        max-width: 1200px;
        margin: auto;
    }
    #burger-table-heading,
    #burger-table-rows,
    .burger-table-row {
        display: flex;
        flex-wrap: wrap;
    }
    #burger-table-heading {
        font-weight: bold;
        padding: 12px;
        border-bottom: 2px solid #ffb89a;
    }
    #burger-table-heading div,
    .burger-table-row div {
        width: 13%;
    }
    .burger-table-row {
        width: 100%;
        padding: 12px;
        border-bottom: 1px solid #f8cfbe;
    }
    #burger-table-heading .order-id,
    .burger-table-row .order-number {
        width: 5%;
    }
    select {
        padding: 12px 6px;
        margin-right: 12px;
    }
    .delete-btn {
        background-color: #0c60b4;
        color: #f8cfbe;
        font-weight: bold;
        border: 2px solid #0c60b4;
        border-radius: 4px;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }
    .delete-btn:hover {
        background-color: transparent;
        color: #0c60b4;
    }
    #lado {
        display: flex;
    }
</style>