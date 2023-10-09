
<template>
  <div>
        <h1>Rock-Paper-Scissors Game</h1>
        
        <p>Player: {{ playerId }}</p>
        <p>Contract Balance: {{ contractBalance }} ETH</p>
        <p>Reveal Time Left: {{ revealTimeLeft }} seconds</p>
        
        <h2>Register Player</h2>
        <button @click="registerPlayer">Register</button>
        
        <h2>Place Bet</h2>
        <input type="number" v-model="betAmount" placeholder="Enter Bet Amount (in ETH)">
        <button @click="placeBet">Place Bet</button>
        
        <h2>Make Move</h2>
        <input type="text" v-model="move" placeholder="Enter Move (rock, paper, or scissors)">
        <button @click="makeMove">Make Move</button>
        
        <h2>Reveal Move</h2>
        <input type="text" v-model="clearMove" placeholder="Enter Clear Move">
        <button @click="revealMove">Reveal Move</button>
        
        <h2>Get Game Outcome</h2>
        <button @click="getOutcome">Get Outcome</button>
    </div>
</template>

<script>
import Web3 from 'web3';
import { EventEmitter } from 'events';
import jsonData from './utils/abi.json';

export default {
    data: {
        playerId: 'Not Registered',
        contractBalance: 0,
        revealTimeLeft: 0,
        betAmount: '',
        move: '',
        clearMove: '',
        contract: {},
        web3: {}
    },

  methods: {
      async registerPlayer() {
          try {
        const accounts = await web3.eth.getAccounts();
        if (accounts.length > 0) {
          this.updatePlayerInfo();
        } else {
          this.networkError = 'No accounts found in MetaMask.';
        }
      } catch (error) {
        this.networkError = 'Error connecting to MetaMask: ' + error.message;
      }
    },
      async placeBet() {
            const accounts = await web3.eth.requestAccounts();
            const player = accounts[0];
            
            await this.contract.methods.play(web3.utils.toWei(this.betAmount, "bnb")).send({ from: player });
            this.updatePlayerInfo();
        },
      async makeMove() {
            const accounts = await web3.eth.requestAccounts();
            const player = accounts[0];
            const moveHash = web3.utils.soliditySha3(player, this.move);
            
            await this.contract.methods.play(moveHash).send({ from: player });
            this.updatePlayerInfo();
        },
      async revealMove() {
            
        const web3 = new Web3(window.ethereum);
            const accounts = await web3.eth.requestAccounts();
            const player = accounts[0];
            
            await this.contract.methods.reveal(this.clearMove).send({ from: player });
            this.updatePlayerInfo();
        },
        async getOutcome() {
            const outcome = await contract.methods.getOutcome().call();
            alert(outcome);
            this.updatePlayerInfo();
        },
      async updatePlayerInfo() {
            const accounts = await web3.eth.requestAccounts();
            const player = accounts[0];
            const playerId = await this.contract.methods.whoAmI().call({ from: player });
            const contractBalance = await this.contract.methods.getContractBalance().call();
            const revealTimeLeft = await this.contract.methods.revealTimeLeft().call();
            
            this.playerId = playerId === "1" ? "Player 1" : playerId === "2" ? "Player 2" : "Not Registered";
            this.contractBalance = web3.utils.fromWei(contractBalance, "bnb");
            this.revealTimeLeft = revealTimeLeft;
        }
    },
    async created() {
        if (window.ethereum) {
            window.web3 = new Web3(ethereum);
            try {
            await window.ethereum.request({ method: 'eth_requestAccounts' })
            ethereum.enable();
        } catch (error) {
            console.log("Error")
        }
        } else { 
            console.log('Non-Ethereum browser detected. You should consider trying MetaMask!');
        }
        const contractAddress = '0xd8b934580fcE35a11B58C6D73aDeE468a2833fa8';
        const contractABI = jsonData;
        this.contract = new web3.eth.Contract(contractABI, contractAddress);

    }
}
</script>

<style>
/* Добавьте стили по вашему усмотрению */
</style>
