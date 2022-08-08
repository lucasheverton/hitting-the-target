<template>
  <canvas width="600" height="400" @contextmenu.prevent></canvas>

  <div class="setting-game">

    <div class="box-setting-game">
      <div class="box-setting-game__level">
        <select id="levelOptions" @change="chooseLevel()">
          <option value="1">Easy</option>
          <option value="2">Medium</option>
          <option value="3">Hard</option>
        </select>
      </div>

      <div class="box-setting-game__behavior-game">
        <button @click="playGame()">
          <img src="@/img/play.svg" alt="Play" title="Play">
          <span>PLAY</span>
        </button>
      </div>
    </div>

    <div class="box-setting-view">
      <div class="box-setting-view__score">
        <p>Score</p>
        <span class="--score" v-text="score"></span>
      </div>
      <div class="box-setting-view__timer">
        <p>Timer</p>
        <span class="--timer" name="timer">00:{{ seconds < 10 ? '0' + seconds : seconds }}</span>
      </div>
    </div>

    <div class="container-score-table">
      <table class="score-table">
        <tbody>
          <tr class="score-table--title">
            <th colspan="2"><b>Tabela de pontos</b></th>
          </tr>
          <tr class="score-table--header">
            <th>Dificuldade</th>
            <th>Score</th>
          </tr>
          <tr v-if="checkScore.length < 1">
            <td>Fácil</td>
            <td>0</td>
          </tr>
          <tr v-else v-for="(item, pos) in checkScore" :key="`points--${pos}`">
            <td v-text="item.level"></td>
            <td v-text="item.score"></td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="box-setting-view__rules">
      <p class="how-to-play">Como Jogar:</p>

      <p>Para jogar o <strong>Acerte ao Alvo</strong> basta apertar o <strong>Play</strong> ou se quiser uma dificuldade
        maior pode escolher um nível.</p>
      <p>Para pontuar é necessário acertar o alvo em amarelo, a cada ponto seu score é aumentado.</p>
      <p>O timer é iniciado quando o jogo começa e a cada 30 segundos, seu score é gravado e zerado.</p>
      <p>Caso você mude o nível no meio do game o timer e o score são zerados também.</p>
    </div>

  </div>
</template>

<script>
export default {
  name: 'Game',
  data() {
    return {
      raio: 10,
      valorX: 0,
      valorY: 0,
      targetRandom: false,
      canvas: null,
      pincel: null,
      selectedLevel: '1',
      namedLevel: '',
      speedTarget: 1000,
      score: 0,
      seconds: 30,
      myInterval: null,
      isFirstGame: true,
      isFirstCount: true,
      countOff: true,
      checkScore: [],

    }
  },
  methods: {
    checkLevel() {
      if (this.selectedLevel == '1') {
        this.namedLevel = 'Fácil';
      } else if (this.selectedLevel == '2') {
        this.namedLevel = 'Médio';
      } else {
        this.namedLevel = 'Difícil';
      }
    },

    chooseLevel() {
      const selectLevel = document.getElementById('levelOptions');
      const valueSelect = selectLevel.options[selectLevel.selectedIndex].value;

      this.selectedLevel = valueSelect;

      if (this.selectedLevel == '0') {
        this.selectedLevel == '1';
      }

      switch (this.selectedLevel) {
        case '1':
          this.speedTarget = 1000;
          break;
        case '2':
          this.speedTarget = 800;
          break;
        case '3':
          this.speedTarget = 600;
          break;
        default:
          break;
      }

      clearInterval(this.myInterval);
      this.seconds = 0;
      this.score = 0;
      this.countOff = false;
      this.isFirstGame = true;
    },

    playGame() {
      if (this.isFirstGame) {
        this.seconds = 30;
        this.countOff = true;
        this.targetRandom = true;
        this.checkLevel();

        if (this.isFirstCount) {
          this.countDown();
          this.isFirstCount = false;
        }

        this.canvas.onclick = this.checkIfYouHit;

        this.myInterval = setInterval(this.createTarget, this.speedTarget);
        this.isFirstGame = false;
      }
    },

    countDown() {
      setInterval(() => {
        if (this.seconds == 1) {
          this.checkScore.push({
            score: this.score,
            level: this.namedLevel
          });
          this.score = 0;
        }

        if (this.seconds > 1 && this.countOff) {
          this.seconds -= 1;
        } else {
          this.seconds = 30;
        }
      }, 1000)
    },

    cleanCanvas() {
      this.pincel.clearRect(0, 0, 600, 400);
      this.pincel.fillStyle = '#222222';
      this.pincel.fillRect(0, 0, 600, 400);
    },

    drawCircle(x, y, raio, cor) {
      this.pincel.fillStyle = cor;
      this.pincel.beginPath();
      this.pincel.arc(x, y, raio, 0, 2 * Math.PI);
      this.pincel.fill();
      this.valorX = x;
      this.valorY = y;
    },

    createTarget() {
      if (this.targetRandom) {
        this.cleanCanvas();
        var x = Math.round(Math.random() * 600);
        var y = Math.round(Math.random() * 400);

        this.drawCircle(x, y, this.raio + 20, 'blue'); // maior círculo
        this.drawCircle(x, y, this.raio + 10, 'red');
        this.drawCircle(x, y, this.raio, 'yellow'); // menor circulo
      }
    },

    checkIfYouHit($event) {
      var x = $event.pageX - this.canvas.offsetLeft;
      var y = $event.pageY - this.canvas.offsetTop;

      if (
        x >= this.valorX - this.raio
        && this.valorX <= x + this.raio
        && y >= this.valorY - this.raio
        && y <= this.valorY + this.raio
        && this.countOff) {

        this.score += 1;
      }
    },

    createTools() {
      const $canvas = document.querySelector('canvas');
      const pincel = $canvas.getContext('2d');

      this.canvas = $canvas;
      this.pincel = pincel

      this.pincel.fillStyle = '#343434';
      this.pincel.fillRect(0, 0, 600, 400);
    }
  },
  mounted() {
    this.createTools();
  }
}
</script>

<style lang="scss">
canvas {
  display: block;
  margin: 0 auto;
  border-radius: 10px;
  box-shadow: 0 0 1em #000;
}

.setting-game {
  display: block;
  margin: 0 auto;
  max-width: 600px;

  .box-setting-game__level,
  .box-setting-game__behavior-game {
    select {
      width: 180px;
      height: 58px;
      padding: 1em;
      text-align: center;
      border-radius: 10px;
      font-size: 1.2em;
      font-family: 'Gluten', cursive;
      background-color: #00a1ff;
      color: #FFFFFF;
      border: none;
      outline: none;
      transition: .3s;
      -webkit-box-sizing: border-box;
      -moz-box-sizing: border-box;
      box-sizing: border-box;
      -webkit-appearance: none;
      -moz-appearance: none;
      background-image: linear-gradient(45deg, transparent 50%, white 50%), linear-gradient(135deg, white 50%, transparent 50%);
      background-position: calc(100% - 26px) calc(1em + 4px), calc(100% - 21px) calc(1em + 4px), calc(100% - 2.5em) 0.5em;
      background-size: 10px 6px, 6px 10px, 1px 1.5em;
      background-repeat: no-repeat;

      &:hover {
        cursor: pointer;
        background-color: #00a1ffbf;
        transition: ease-in-out .3s;
      }

      option {
        background-color: #FFF;
        color: #000;
      }
    }

    button {
      width: 180px;
      height: 58px;
      padding: 1em;
      text-align: center;
      border-radius: 10px;
      font-size: 1.2em;
      font-family: 'Gluten', cursive;
      display: flex;
      place-items: center;
      justify-content: center;
      background-color: #0a8300;
      color: #FFFFFF;
      transition: .3s;
      border: none;
      outline: none;

      &:hover {
        cursor: pointer;
        background-color: #0a8300b5;
        transition: ease-in-out .3s;
      }

      img {
        width: 20px;
        height: auto;
        margin-right: 1em;
      }
    }
  }

  .box-setting-view__score,
  .box-setting-view__timer {
    p {
      margin: 0;
      padding-bottom: 1em;
      font-size: 1.325em;
      color: #6e0c0c;
      font-weight: 500;
    }

    span {
      font-size: 1.4em;
      color: #000;
      padding: 8px 1em;
    }

    .--score {
      color: red;
    }

    .--timer {
      border-radius: 10px;
      background-color: #f1f1f1;
      color: #ff7b00;
    }
  }

  .container-score-table {
    width: 340px;
    max-height: 220px;
    overflow-y: scroll;
    overflow-x: hidden;
    padding-right: 6px;
    margin: 3em auto 0 auto;

    &::-webkit-scrollbar {
      width: 6px;
    }

    &::-webkit-scrollbar-thumb {
      background-color: #a7a7a7;
      border-radius: 20px;
    }

    .score-table {
      width: inherit;

      td,
      th {
        border: 1px solid #000000;
        border-radius: 5px;
        text-align: left;
        padding: 10px;
        text-align: center;
      }

      tr:nth-child(even) {
        background-color: #e7e7e7;
        color: #000000;
      }

      tr:nth-child(odd) {
        color: #000000;
      }

      tr.score-table--title {
        background-color: #460072;
        color: #ffffff;
      }

      tr.score-table--header {
        background-color: #000000;
        color: #FFFFFF;
      }

    }
  }

  .box-setting-game,
  .box-setting-view {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    justify-items: center;
    padding-top: 1.5em;
    text-align: center;
  }

  .box-setting-view__rules {
    padding: 1.5em 1em 0 1em;

    .how-to-play {
      font-size: 1.5em;
      font-weight: 500;
      box-shadow: #000;
    }

    p {
      font-size: 1em;
      line-height: 1.425em;
    }
  }

}
</style>
