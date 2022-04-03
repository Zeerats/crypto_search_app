<template>
    <div class="container">
        <h1>CRYPTO SEARCH 1.0</h1>
        <div class="coin-submit" v-on:keyup.enter="addAsset">
            <input class="coin-input" v-model="inputAsset" type="text" placeholder="Crypto name...">
            <h2 class="toggle-button" @click="addAsset">ADD COIN/TOKEN</h2>
            <p class="error-message">{{ warningText }}</p>
        </div>
        <div class="column-wrapper">
            <div class="column">
                <div v-for="(asset, index) in assets" :key="index">
                    <!-- 
                        TO-DO:
                        The socket should also be passed in as prop, so only one connection 
                        is used and the function can be executed on mounting of the 
                        different components whenever a new one is added.
                    -->
                    <Card :coin="asset"/>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import Card from './Card.vue'

export default {
    name: 'Wallet',
    components: { Card },
    data() {
        return {
            warningText: '',
            inputAsset: '',
            assets: [],
            supportedCoins: ['btc', 'eth', 'ada', 'bnb', 
                             'dot', 'iota', 'ltc', 'sol', 
                             'uni', 'vet', 'xlm', 'xmr']
        }
    },
    methods: {
        addAsset() {
            let userInput = this.inputAsset.toLowerCase()
            if (this.supportedCoins.includes(userInput) && !this.assets.includes(userInput)) {
                this.assets.push(userInput)
                this.inputAsset = ''
                this.warningText = ''
            } else if (this.assets.includes(userInput)) {
                this.warningText = "Coin is already being displayed."
                this.inputAsset = ''
            } else {
                this.warningText = "Coin is either not supported or does not exist."
                this.inputAsset = ''
            }
        }
    }
}
</script>

<style scoped>
h1 {
    color:rgb(0, 247, 255);
}
h2 {
    margin: 0;
}
.coin-submit {
    display: flex;
    flex-direction: column;
    align-items: center;
}
.toggle-button {
    color: #707070;
    width: 492px;
    margin-bottom: 20px;
    padding: 10px 0;
    border: solid;
    border-width: 4px;
    border-radius: 10px;
    cursor: pointer;
    transition: 0.1s;
    user-select: none;
}
.toggle-button:hover {
    color: #cccccc;
}
.coin-input {
    background-color: black;
    color: white;
    padding: 10px;
    border: none;
    border-radius: 10px;
    margin: 20px;
    outline: none;
    width: 500px;
    box-sizing: border-box;
    text-align: center;
    font-family: Montserrat;
    font-size: 30px;
}
.error-message {
    color: crimson;
    margin: 0;
}
</style>