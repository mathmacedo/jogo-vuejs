<template>
  <div id="app">
    <section class="row">
        <div class="small-6 columns">
            <h1 class="text-center">Você</h1>
            <div class="healthbar">
                <div class="healthbar text-center" style="background-color: green; margin: 0; color: white;" :style="{width: playerHealth + '%'}"> {{ playerHealth >= 0 ? playerHealth : 0}}
                </div>
            </div>
        </div>
        <div class="small-6 columns">
            <h1 class="text-center">Monstro</h1>
            <div class="healthbar">
                <div class="healthbar text-center" style="background-color: green; margin: 0; color: white;" :style="{width: monsterHealth + '%'}">{{ monsterHealth  >= 0 ? monsterHealth : 0}}
                </div>
            </div>
        </div>
    </section>
    <section class="row controls" v-if="gameIsRunning == false">
        <div class="small-12 columns">
            <button id="start-game" @click="startGame">Começar novo jogo</button>
        </div>
    </section>
    <section class="row controls" v-else>
        <div class="small-12 columns">
            <button id="attack" @click="attack">Atacar</button>
            <button id="special-attack" @click="specialAttack">Ataque Especial</button>
            <button id="heal" @click="heal">Curar-se</button>
            <button id="give-up" @click="giveUp">Desistir</button>
        </div>
    </section>
    <section class="row log" v-if="turns.length > 0">
        <div class="small-12 columns">
            <ul>
                <li v-for="turn in turns" v-bind:key="turn" :class="{'player-turn': turn.isPlayer, 'monster-turn': !turn.isPlayer}">
					{{ turn.text }}
                </li>
            </ul>
        </div>
    </section>
  </div>
</template>

<script lang="ts">

  export default {
		name: 'app',
		data() {
			return {
        playerHealth: 100,
        monsterHealth: 100,
        gameIsRunning: false,
        turns : []
			}
		},
		components: {
		},
		methods: {
      startGame() {
        this.gameIsRunning = true;
        this.playerHealth = 100;
        this.monsterHealth = 100;
        this.turns = [];
      },

      attack() {
        var damage = this.calculateDamage(3, 10);
        this.monsterHealth -= damage;
        this.turns.unshift({
          isPlayer: true,
          text: 'Você acertou o monstro com dano de ' + damage
        });

        if (this.checkWin()) {
          return ;
        }

        this.monsterAttacks();
      },

      specialAttack() {
        var damage = this.calculateDamage(10, 20);
        this.monsterHealth -= damage;
        this.turns.unshift({
          isPlayer: true,
          text: 'Você acertou o monstro com dano forte de ' + damage
        });
        
        if (this.checkWin()) {
          return;
        }

        this.monsterAttacks();
		  },

      heal() {
        if (this.playerHealth <=90) {
          this.playerHealth += 10;
        } else {
          this.playerHealth = 100;
        }

        this.turns.unshift({
          isPlayer: true,
          text: 'Você curou sua vida em 10'
        });

        this.monsterAttacks();	
		  },

      giveUp() {
			  this.gameIsRunning = false ;
		  },

      monsterAttacks() {
        var damage = this.calculateDamage(5, 12);
        this.playerHealth -= damage;
        this.checkWin();
        
        this.turns.unshift({
          isPlayer: false,
          text: 'O monstro te acertou com dano de  ' + damage
        });
      },

      calculateDamage(min , max) {
			  return Math.max(Math.floor(Math.random() * max) + 1, min);
		  },

		  checkWin() {
        if (this.monsterHealth <= 0) {
          if (confirm("Você venceu. Parabéns ! Novo Jogo ?")) {
            this.startGame();
          } else {
            this.gameIsRunning = false;
          }
          return true;
        } else if (this.playerHealth <=0) {
          if (confirm("Você perdeu :( . Gostaria de tentar novamente ?")) {
            this.startGame();
          } else {
            this.gameIsRunning = false;
          }
          return true;
        }
        return false;
		  }
		}
	}
</script>

<style>
  .text-center {
    text-align: center;
  }

  .healthbar {
    width: 80%;
    height: 40px;
    background-color: #eee;
    margin: auto;
    transition: width 500ms;
  }

  .controls, .log {
    margin-top: 30px;
    text-align: center;
    padding: 10px;
    border: 1px solid #ccc;
    box-shadow: 0px 3px 6px #ccc;
  }

  .turn {
    margin-top: 20px;
    margin-bottom: 20px;
    font-weight: bold;
    font-size: 22px;
  }

  .log ul {
    list-style: none;
    font-weight: bold;
    text-transform: uppercase;
  }

  .log ul li {
    margin: 5px;
  }

  .log ul .player-turn {
    color: blue;
    background-color: #e4e8ff;
  }

  .log ul .monster-turn {
    color: red;
    background-color: #ffc0c1;
  }

  button {
    font-size: 20px;
    background-color: #eee;
    padding: 12px;
    box-shadow: 0 1px 1px black;
    margin: 10px;
  }

  #start-game {
    background-color: #aaffb0;
  }

  #start-game:hover {
    background-color: #76ff7e;
  }

  #attack {
    background-color: #ff7367;
  }

  #attack:hover {
    background-color: #ff3f43;
  }

  #special-attack {
    background-color: #ffaf4f;
  }

  #special-attack:hover {
    background-color: #ff9a2b;
  }

  #heal {
    background-color: #aaffb0;
  }

  #heal:hover {
    background-color: #76ff7e;
  }

  #give-up {
    background-color: #ffffff;
  }

  #give-up:hover {
    background-color: #c7c7c7;
  }
</style>
