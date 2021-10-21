<template>
    <div style="margin-left:30%; margin-right:30%; margin-top:5%; margin-bottom:5%">
        <h2>Enter your players' names in ascending power level</h2>
        <p>(separated by commas no spaces)</p>
        <b-input v-model="players_str"></b-input>
        <br>
        <b-button variant='primary' @click="update_players">Update Players List</b-button>
        <b-button @click="report_scores">Report Scores</b-button>
        <b-button variant='success' @click="generate_matchups">Generate Matchups!</b-button>
        <br>
        <br>
        <div>
            <h3>Off-Court</h3>
            <div class='drop-zone' @drop='onDrop($event, 0)' @dragover.prevent @dragenter.prevent>
                <div v-for='item in off_court' :key='item.title' class='drag-el' draggable @dragstart='startDrag($event, item)'>
                    {{ item.name }}
                </div>
            </div>
            <h3>Court 1</h3>
            <b-row>
                <b-col>
                    <div class='drop-zone' @drop='onDrop($event, 1, 1)' @dragover.prevent @dragenter.prevent>
                        <div v-for='item in court_1_side_1' :key='item.title' class='drag-el' draggable @dragstart='startDrag($event, item)'>
                            {{ item.name }}
                        </div>
                    </div>
                </b-col>
                <b-col>
                    <div class='drop-zone' @drop='onDrop($event, 1, 2)' @dragover.prevent @dragenter.prevent>
                        <div v-for='item in court_1_side_2' :key='item.title' class='drag-el' draggable @dragstart='startDrag($event, item)'>
                            {{ item.name }}
                        </div>
                    </div>
                </b-col>
            </b-row>
            <h3>Court 2</h3>
            <b-row>
                <b-col>
                    <div class='drop-zone' @drop='onDrop($event, 2, 1)' @dragover.prevent @dragenter.prevent>
                        <div v-for='item in court_2_side_1' :key='item.title' class='drag-el' draggable @dragstart='startDrag($event, item)'>
                            {{ item.name }}
                        </div>
                    </div>
                </b-col>
                <b-col>
                    <div class='drop-zone' @drop='onDrop($event, 2, 2)' @dragover.prevent @dragenter.prevent>
                        <div v-for='item in court_2_side_2' :key='item.title' class='drag-el' draggable @dragstart='startDrag($event, item)'>
                            {{ item.name }}
                        </div>
                    </div>
                </b-col>
            </b-row>
            <h3>Court 3</h3>
            <b-row>
                <b-col>
                    <div class='drop-zone' @drop='onDrop($event, 3, 1)' @dragover.prevent @dragenter.prevent>
                        <div v-for='item in court_3_side_1' :key='item.title' class='drag-el' draggable @dragstart='startDrag($event, item)'>
                            {{ item.name }}
                        </div>
                    </div>
                </b-col>
                <b-col>
                    <div class='drop-zone' @drop='onDrop($event, 3, 2)' @dragover.prevent @dragenter.prevent>
                        <div v-for='item in court_3_side_2' :key='item.title' class='drag-el' draggable @dragstart='startDrag($event, item)'>
                            {{ item.name }}
                        </div>
                    </div>
                </b-col>
            </b-row>
            <h3>Court 4</h3>
            <b-row>
                <b-col>
                    <div class='drop-zone' @drop='onDrop($event, 4, 1)' @dragover.prevent @dragenter.prevent>
                        <div v-for='item in court_4_side_1' :key='item.title' class='drag-el' draggable @dragstart='startDrag($event, item)'>
                            {{ item.name }}
                        </div>
                    </div>
                </b-col>
                <b-col>
                    <div class='drop-zone' @drop='onDrop($event, 4, 2)' @dragover.prevent @dragenter.prevent>
                        <div v-for='item in court_4_side_2' :key='item.title' class='drag-el' draggable @dragstart='startDrag($event, item)'>
                            {{ item.name }}
                        </div>
                    </div>
                </b-col>
            </b-row>
        </div>
    </div>
</template>

<script>
import Court from '../components/Court.vue'
export default {
    data() {
        return {
            players_str: "",
            players: []
        }
    },
    computed: {
        
        off_court(){
            return this.players.filter(player => player.placement.court === 0)
        },
        court_1_side_1(){
            return this.players.filter(player => player.placement.court === 1 && player.placement.side === 1)
        },
        court_1_side_2(){
            return this.players.filter(player => player.placement.court === 1 && player.placement.side === 2)
        },
        court_2_side_1(){
            return this.players.filter(player => player.placement.court === 2 && player.placement.side === 1)
        },
        court_2_side_2(){
            return this.players.filter(player => player.placement.court === 2 && player.placement.side === 2)
        },
        court_3_side_1(){
            return this.players.filter(player => player.placement.court === 3 && player.placement.side === 1)
        },
        court_3_side_2(){
            return this.players.filter(player => player.placement.court === 3 && player.placement.side === 2)
        },
        court_4_side_1(){
            return this.players.filter(player => player.placement.court === 4 && player.placement.side === 1)
        },
        court_4_side_2(){
            return this.players.filter(player => player.placement.court === 4 && player.placement.side === 2)
        }
    },
    methods: {
        update_players() {
            var simple_list = this.players_str.split(",");
            this.players = []
            for (let i=0;i<simple_list.length;i++){
                this.players.push(
                    {
                        id: i,
                        name: simple_list[i],
                        placement: {court: 0, side:0}
                    }
                )
            }
        },
        startDrag: (evt, item) => {
            console.log("START")
            evt.dataTransfer.dropEffect = 'move'
            evt.dataTransfer.effectAllowed = 'move'
            evt.dataTransfer.setData('itemID', item.id)
        },
        onDrop (evt, court, side) {
            const itemID = evt.dataTransfer.getData('itemID')
            console.log(court, itemID)
            this.players[itemID].placement.court = court
            this.players[itemID].placement.side = side
            console.log(court)
            console.log(this.players)
        },
        generate_matchups() {
            var num_players = this.players.length
            var num_off_court = this.off_court.length
            if (num_players >= 16){
                var num_doubles_matches = 4
                for (let i=0; i<4; i++) {
                    for (let j=0; j<2; j++) {
                        player1_id = this.off_court[Math.floor(Math.random() * ((num_off_court/2).parseInt() + 1))].id
                        player2_id = this.off_court[Math.floor(Math.random() * ((num_off_court-1) - (num_off_court/2).parseInt() + 1) + (num_off_court/2).parseInt())].id
                        player1 = players.find(player => player.id === player1_id)
                        player1.placement.court = i+1
                        player1.placement.side = j+1
                        player2 = players.find(player => player.id === player2_id)
                        player2.placement.court = i+1
                        player2.placement.side = j+1
                    }
                }
            } else {
                var num_doubles = Math.floor(16-num_off_court)/4
                for (let i=0; i<num_doubles; i++) {
                    for (let j=0; j<2; j++) {
                        var player1_id = this.off_court[Math.floor(Math.random() * ((num_off_court/2).parseInt() + 1))].id
                        var player2_id = this.off_court[Math.floor(Math.random() * ((num_off_court-1) - (num_off_court/2).parseInt() + 1) + (num_off_court/2).parseInt())].id
                        var player1 = players.find(player => player.id === player1_id)
                        player1.placement.court = i+1
                        player1.placement.side = j+1
                        var player2 = players.find(player => player.id === player2_id)
                        player2.placement.court = i+1
                        player2.placement.side = j+1
                    }
                }
                if (this.off_court.length == 2) {
                    var player1_id = this.off_court[0].id
                    var player2_id = this.off_court[1].id
                    var player1 = players.find(player => player.id === player1_id)
                    player1.placement.court = num_doubles + 1
                    player1.placement.side = 1
                    var player2 = players.find(player => player.id === player2_id)
                    player2.placement.court = num_doubles+1
                    player2.placement.side = 2
                }
            }
        }
    }
}
</script>
<style scoped>
  .drop-zone {
    background-color: #eee;
    margin-bottom: 10px;
    padding: 30px;
  }

  .drag-el {
    background-color: #fff;
    margin-bottom: 10px;
    padding: 5px;
  }
</style>