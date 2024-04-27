<template>
    <div class="flex flex-col">

        <div class="flex flex-col items-start">
            <label for="num-rolls" class="text-lg font-medium">Anzahl der Würfe:</label>
            <input v-on:keyup.enter="diceRolled(1)" type="number" id="num-rolls" class="border-gray-300 border rounded-md px-3 py-2 w-full" min="1" max="1000000" v-bind:keyup=enforceMinMax() v-model="numberOfRolls" @input="removeLeadingZeros">
            <label v-if="warningRollsLow" class="text-red-700">Du musst mindestens 1 eingeben.</label>
            <label v-if="warningRollsHigh" class="text-red-700">Du darfst maximal 100.000 eingeben.</label>
            <label v-if="warningRollsMax" class="text-red-700">Die maximale Anzahl der Würfe insgesamt ist 1.000.000.</label>
        </div>

        <div class="flex flex-col">
            <button type="button" @click="diceRolled(1)" class="button text-white rounded-md px-4 py-2 mt-4 mb-2">Würfeln</button>
        </div>

        <div class="total-rolls-container marg-top" v-if="this.total||this.percent">
            <lable>Anzahl der Würfe insgesamt: {{this.numberOfRollsTotal}}</lable>
        </div>

        <div v-if="this.total||this.percent" ref="container" class="space-y-2 lableContainer marg-top">
            <label>Auf dem Würfel sind:</label>
            <div>
                <label class="text-lg font-medium redInput w-full block">{{ 7 }} rote Seiten</label>
            </div>
            <div>
                <label class="text-lg font-medium greenInput w-full block">{{ 5 }} grüne Seiten</label>
            </div>
            <div>
                <label class="text-lg font-medium yellowInput w-full block">{{ 5 }} gelbe Seiten</label>
            </div>
            <div>
                <label class="text-lg font-medium blueInput w-full block">{{ 3 }} blaue Seiten</label>
            </div>
        </div>


        <div class="flex flex-col items-start">
            <label for="num-rolls" class="text-lg font-medium">Anzahl der Würfe:</label>
            <input v-on:keyup.enter="diceRolled(2)" type="number" id="num-rolls" class="border-gray-300 border rounded-md px-3 py-2 w-full" min="1" max="1000000" v-bind:keyup=enforceMinMax2() v-model="numberOfRolls2" @input="removeLeadingZeros">
            <label v-if="warningRollsLow2" class="text-red-700">Du musst mindestens 1 eingeben.</label>
            <label v-if="warningRollsHigh2" class="text-red-700">Du darfst maximal 100.000 eingeben.</label>
            <label v-if="warningRollsMax2" class="text-red-700">Die maximale Anzahl der Würfe insgesamt ist 1.000.000.</label>
        </div>

        <div class="flex flex-col">
            <button type="button" @click="diceRolled(2)" class="button text-white rounded-md px-4 py-2 mt-4 mb-2">Würfeln</button>
        </div>

        <div class="total-rolls-container marg-top" v-if="this.total||this.percent">
            <lable>Anzahl der Würfe insgesamt: {{this.numberOfRollsTotal2}}</lable>
        </div>
    </div>
</template>


<script>
    import {defineComponent, createApp} from 'vue'
    import { Mode } from '../assets/enum/mode.js';
    export default {
    props: {
        default: {
            type: String,
            default: Mode.NONE, // Standardwert setzen
            validator: value => Object.values(Mode).includes(value)
        }
    },

    watch: {
        default(newVal, oldVal) {
            console.log('Modus geändert von', oldVal, 'zu', newVal);
            if(oldVal == newVal) return;
            if(newVal == Mode.EASY) {
                this.easy = true;
                this.total = false;
                this.percent = false;
                this.numberOfRolls = 0;
                this.warningRollsMax = false;
            } 
            if(newVal == Mode.TOTAL) {
                this.total = true;
                this.easy = false;
                this.percent = false;
                this.numberOfRolls = 0;
                this.warningRollsMax = false;
            } 
            if(newVal == Mode.PERCENT) {
                this.percent = true;
                this.easy = false;
                this.total = false;
                this.numberOfRolls = 0;
                this.warningRollsMax = false;
            } 
        }
    },

    data() {
        return {
        easy: false,
        total: false,
        percent: false,
        numberOfRolls: '',
        numberOfRolls2: '',
        numberOfRollsTotal: '',
        numberOfRollsTotal2: '',
        totalSides: 20,
        rolls: [],
        rolls2: [],
        warningRollsHigh: false,
        warningRollsLow: false,
        warningRollsMax: false,
        warningRollsHigh2: false,
        warningRollsLow2: false,
        warningRollsMax2: false,
        diceSidesInfo:  [
            {rank: 0, color:'red', text: 'Rote Seiten: ', classColor: 'redInput' , sides: 7},
            {rank: 1, color:'green', text: 'Grüne Seiten: ', classColor: 'greenInput' , sides: 5},
            {rank: 2, color:'yellow', text: 'Gelbe Seiten: ', classColor: 'yellowInput' , sides: 5},
            {rank: 3, color:'blue', text: 'Blaue Seiten: ', classColor: 'blueInput' , sides: 3},
        ],
        }
    },
    methods: {
        diceRolled(number) {
            const rolls = [];
            if(number == 1) {
                if (this.numberOfRolls == 0) {
                    this.warningRollsLow = true;
                }

                if (this.warningRollsLow || this.warningRollsHigh) {
                    this.rolls = rolls;
                    return;
                }

                if ((this.numberOfRolls + this.numberOfRollsTotal) > 1000000) {
                    this.warningRollsMax = true;
                    return;
                }
                else {
                    this.warningRollsMax = false;
                }
            }

            if(number == 2) {
                if (this.numberOfRolls2 == 0) {
                    this.warningRollsLow2 = true;
                }

                if (this.warningRollsLow2 || this.warningRollsHigh2) {
                    this.rolls2 = rolls;
                    return;
                }

                if ((this.numberOfRolls2 + this.numberOfRollsTotal2) > 1000000) {
                    this.warningRollsMax2 = true;
                    return;
                }
                else {
                    this.warningRollsMax2 = false;
                }
            }  

            let rangeRed = 7;
            let rangeGreen = 12;
            let rangeYellow = 17;
            let rangeBlue = 20;

            if(number == 1){
                for (let i = 0; i < this.numberOfRolls; i++) {
                    const randomRoll = Math.floor(Math.random() * this.totalSides) + 1
                    if (randomRoll <= rangeRed) {
                        rolls.push('red')
                    } else if (randomRoll <= rangeGreen) {
                        rolls.push('green')
                    } else if (randomRoll <= rangeYellow){
                        rolls.push('yellow')
                    } else if (randomRoll <= rangeBlue) {
                        rolls.push('blue')
                    }
                }
                this.rolls = rolls

                this.numberOfRollsTotal = this.numberOfRolls + this.numberOfRollsTotal;
            
                this.$emit('rollDice');
            }
            if(number == 2){
                for (let i = 0; i < this.numberOfRolls2; i++) {
                    const randomRoll = Math.floor(Math.random() * this.totalSides2) + 1
                    if (randomRoll <= rangeRed) {
                        rolls.push('red')
                    } else if (randomRoll <= rangeGreen) {
                        rolls.push('green')
                    } else if (randomRoll <= rangeYellow){
                        rolls.push('yellow')
                    } else if (randomRoll <= rangeBlue) {
                        rolls.push('blue')
                    }
                }
                this.rolls2 = rolls

                this.numberOfRollsTotal2 = this.numberOfRolls2 + this.numberOfRollsTotal2;
            
                this.$emit('rollDice2');
            }
        },
        enforceMinMax() {
            
            if (this.numberOfRolls > 100000) {
                this.warningRollsHigh = true;
            } 
            else if(this.numberOfRolls > 0) {
                this.warningRollsHigh = false;
                this.warningRollsLow = false;
            }
        },
        enforceMinMax2() {
            
            if (this.numberOfRolls2 > 100000) {
                this.warningRollsHigh2 = true;
            } 
            else if(this.numberOfRolls2 > 0) {
                this.warningRollsHigh2 = false;
                this.warningRollsLow2 = false;
            }
        },
        removeLeadingZeros(event) {
            event.target.value = event.target.value.replace(/^0+/, '');
        },
        setTotalRollsZero() {
            this.numberOfRollsTotal = 0;
            this.numberOfRolls = 0;
            this.warningRollsMax = false;

            this.numberOfRollsTotal2 = 0;
            this.numberOfRolls2 = 0;
            this.warningRollsMax2 = false;
        },
       
    }
}
</script>
