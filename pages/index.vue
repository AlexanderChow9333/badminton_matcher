<template>
    <b-row style="margin-left:20%; margin-right:20%; margin-top:5%; margin-bottom:2%">
        <b-col cols="8">
            <h2>Enter your players' names in ascending power level</h2>
            <p>(separated by commas no spaces)</p>
            <b-input v-model="players_str"></b-input>
            <br>
            <b-button style="margin:10px;" variant='primary' @click="update_players">Update Players List</b-button>
            <b-button v-if="players.filter(player => player.placement.court !== 0).length > 1" style="margin:10px;" v-b-modal.scores_modal>Report Scores</b-button>
            <b-button v-else disabled=true>Report Scores</b-button>
                <b-modal id="scores_modal" title="Report the scores!" ok-disabled=true>
                    <div v-if="court_1_side_1.length > 0 && court_1_side_2.length > 0">
                        <h4>Court 1</h4>
                        <b-input v-model="score_inputs.court1"></b-input>
                    </div>
                    <div v-if="court_2_side_1.length > 0 && court_2_side_2.length > 0">
                        <h4>Court 2</h4>
                        <b-input v-model="score_inputs.court2"></b-input>
                    </div>
                    <div v-if="court_3_side_1.length > 0 && court_3_side_2.length > 0">
                        <h4>Court 3</h4>
                        <b-input v-model="score_inputs.court3"></b-input>
                    </div>
                    <div v-if="court_4_side_1.length > 0 && court_4_side_2.length > 0">
                        <h4>Court 4</h4>
                        <b-input v-model="score_inputs.court4"></b-input>
                    </div>
                    <b-button style="margin:10px;" variant='primary' @click="report_scores">Submit</b-button>
                </b-modal>
            <b-button style="margin:10px;" variant='success' @click="generate_matchups">Generate Matchups!</b-button>
            <br>
            <br>
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
        </b-col>
        <b-col cols="1"></b-col>
        <b-col style="background-color: #b5edb2;" cols="3">
            <h2 style="text-align: center; margin-top:10px;">Results</h2>
            <div v-for='(score, index) in scores' :key='index'>
                <p style="text-align: center">{{ score.side1 + " VS " + score.side2 + ": " + score.result }}</p>
                <hr>
            </div>
            <!-- {{scores}} -->
        </b-col>
    </b-row>
</template>

<script>
import Court from '../components/Court.vue'
export default {
    data() {
        return {
            players_str: "",
            players: [],
            score_inputs: {
                court1: "",
                court2: "",
                court3: "",
                court4: ""
            },
            scores: []
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
            this.update_players()
            var num_players = this.players.length
            if (num_players >= 16){
                for (let i=0; i<4; i++) {
                    for (let j=0; j<2; j++) {
                        var temp_off_court = this.off_court
                        var player1_id = temp_off_court[Math.floor(Math.random() * (parseInt(temp_off_court.length/2)))].id
                        var player2_id = temp_off_court[Math.floor(Math.random() * ((temp_off_court.length) - parseInt(temp_off_court.length/2)) + parseInt(temp_off_court.length/2))].id
                        player1 = this.players.find(player => player.id === player1_id)
                        player1.placement.court = i+1
                        player1.placement.side = j+1
                        player2 = this.players.find(player => player.id === player2_id)
                        player2.placement.court = i+1
                        player2.placement.side = j+1
                    }
                }
            } else {
                var num_doubles = Math.floor((this.off_court.length)/4)
                console.log(num_doubles)
                for (let i=0; i<num_doubles; i++) {
                    for (let j=0; j<2; j++) {
                        var temp_off_court = this.off_court
                        console.log(temp_off_court)
                        var player1_id = temp_off_court[Math.floor(Math.random() * (parseInt(temp_off_court.length/2)))].id
                        console.log("id1", player1_id)
                        var player2_id = temp_off_court[Math.floor(Math.random() * ((temp_off_court.length) - parseInt(temp_off_court.length/2)) + parseInt(temp_off_court.length/2))].id
                        console.log("id2", player2_id)
                        var player1 = this.players.find(player => player.id === player1_id)
                        player1.placement.court = i+1
                        player1.placement.side = j+1
                        console.log("finished p1")
                        var player2 = this.players.find(player => player.id === player2_id)
                        player2.placement.court = i+1
                        player2.placement.side = j+1
                        console.log("finished p2")
                        console.log("players", player1, player2)
                    }
                }
                if (this.off_court.length == 2 || this.off_court.length == 3) {
                    console.log("singles court starting")
                    var random_index = Math.floor(Math.random()*this.off_court.length)
                    var player1_id = this.off_court[random_index].id
                    try {
                        var player2_id = this.off_court[random_index+1].id
                    } catch(err) {
                        var player2_id = this.off_court[random_index-1].id
                    }
                    var player1 = this.players.find(player => player.id === player1_id)
                    player1.placement.court = num_doubles + 1
                    player1.placement.side = 1
                    var player2 = this.players.find(player => player.id === player2_id)
                    player2.placement.court = num_doubles+1
                    player2.placement.side = 2
                }
            }
        },
        report_scores() {
            console.log(this.score_inputs)
            if(this.score_inputs.court1 !== "") {
                console.log("court1 input", this.score_inputs.court1)
                var side1 = []
                for (let i=0;i<this.court_1_side_1.length;i++){
                    side1.push(this.court_1_side_1[i].name)
                }
                var side2 = []
                for (let i=0;i<this.court_1_side_1.length;i++){
                    side2.push(this.court_1_side_2[i].name)
                }
                this.scores.unshift({
                    side1: side1,
                    side2: side2,
                    result: this.score_inputs.court1
                })
            }
            if(this.score_inputs.court2 !== "") {
                console.log("court2 input")
                var side1 = []
                for (let i=0;i<this.court_2_side_1.length;i++){
                    side1.push(this.court_2_side_1[i].name)
                }
                var side2 = []
                for (let i=0;i<this.court_2_side_1.length;i++){
                    side2.push(this.court_2_side_2[i].name)
                }
                this.scores.unshift({
                    side1: side1,
                    side2: side2,
                    result: this.score_inputs.court2
                })
            }
            if(this.score_inputs.court3 !== "") {
                console.log("court3 input")
                var side1 = []
                for (let i=0;i<this.court_3_side_1.length;i++){
                    side1.push(this.court_3_side_1[i].name)
                }
                var side2 = []
                for (let i=0;i<this.court_3_side_1.length;i++){
                    side2.push(this.court_3_side_2[i].name)
                }
                this.scores.unshift({
                    side1: side1,
                    side2: side2,
                    result: this.score_inputs.court3
                })
            }
            if(this.score_inputs.court4 !== "") {
                console.log("court4 input")
                var side1 = []
                for (let i=0;i<this.court_4_side_1.length;i++){
                    side1.push(this.court_4_side_1[i].name)
                }
                var side2 = []
                for (let i=0;i<this.court_4_side_1.length;i++){
                    side2.push(this.court_4_side_2[i].name)
                }
                this.scores.unshift({
                    side1: side1,
                    side2: side2,
                    result: this.score_inputs.court4
                })
            }
        }
    }
}
</script>
<style scoped>
  .drop-zone {
    background-color: #b5edb2;
    margin-bottom: 10px;
    padding: 30px;
  }

  .drag-el {
    background-color: #fff;
    margin-bottom: 10px;
    padding: 5px;
  }
</style>