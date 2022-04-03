<template>
    <div class="card">
        <div class="info">
            <div class="coin-info">
                <img class="icon" :src="iconImg">
                <h2>{{ coin.toUpperCase() }}</h2>
                <h2 v-bind:style="{color: priceColor,}" class="price">{{ value }} $</h2>
            </div>
            <!--
                TO-DO:
                Should pass in the current connection status to know if coin is being fetched correctly.
             -->
            <p class="title">Price is currently being updated through the Binance WebSocket. Update interval is <strong>Real Time</strong>.</p>
            <hr>
            <div class="asset">
                <h4><strong>{{ coin.toUpperCase() }}</strong> BALANCE</h4>
                <h2>{{ cryptoBalance }} {{ coin.toUpperCase() }}</h2>
            </div>
            <div class="asset">
                <h4>FIAT BALANCE (USD)</h4>
                <h2>{{ usdBalance }} $</h2>
            </div>
            <div class="asset">
                <h4>FIAT BALANCE (EUR)</h4>
                <h2>{{ eurBalance }} €</h2>
            </div>
            <input type="text" class="input-field" v-model="cryptoBalance" @keypress="isNumber($event)" placeholder="0.00" />
        </div>
        <div class="buttons">
            <div class="button green" @click="inputQuantity">
                <h2>SAVE</h2>
            </div>
            <div class="button red" @click="deleteQuantity">
                <h2>RESET</h2>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'Card',
    props: ['coin'],
    data() {
        return {
            value: parseFloat(0).toFixed(2),
            cryptoBalance: null,
            quantity: 0,
            priceColor: '',
            iconImg: null
        }
    },
    methods: {
        /*
        TO-DO:
        Breaks when using more than one point.
        */
        isNumber: function(evt) {
            evt = (evt) ? evt : window.event;
            var charCode = (evt.which) ? evt.which : evt.keyCode;
            if ((charCode > 31 && (charCode < 48 || charCode > 57)) && charCode !== 46) {
                evt.preventDefault();;
            } else {
                return true;
            }
        },
        inputQuantity() {
            this.quantity++
        },
        deleteQuantity() {
            this.quantity == 0
        }
    },
    computed: {
        usdBalance: function () {
            let result = this.cryptoBalance * this.value
            return parseFloat(result).toFixed(2)
        },
        eurBalance: function () {
            let result = this.usdBalance * 0.91
            return parseFloat(result).toFixed(2)
        }
    },
    beforeMount: function() {
        let iconPath = require('@/assets/coins/' + this.coin + '.svg')
        this.iconImg = iconPath
    },
    /* Handle WebSocket connection */
    beforeCreate: function() {
        let lastValue = null
        console.log("Starting connection to WebSocket Server")
        let connection = new WebSocket("wss://stream.binance.com:9443/ws/" + this.coin +"usdt@trade")
        connection.onmessage = (event) => {
            let priceData = JSON.parse(event.data)
            let newValue = parseFloat(priceData.p).toFixed(2)
            this.value = newValue
            this.priceColor = !lastValue || lastValue === newValue ? 'black' : newValue > lastValue ? 'green' : 'red'
            lastValue = newValue
       }
        connection.onopen = function(event) {
        console.log("Successfully connected to the Binance WS server.")
        }
    }
}
</script>

<style scoped>
strong {
    font-weight: 900;
}
hr {
    border: 4px solid rgb(0, 0, 0, .1);
}
h4 {
    font-weight: 400;
}
.icon {
    width: 70px;
    margin-right: 20px;
}
::placeholder {
    color: #202020;
}
.price {
    margin-left: auto;
}
</style>