<template>
  <div class="main" :style="mainStyle">
    <div class="ground" :style="battleGroundStyle">
      <div class="start-view" v-if="!isStarted">
        <div v-if="isLose" class="level__info">
          <h3 class="title">Score: {{ scoreGame }} Level</h3>
          <button @click="startNewGame" class="success">New game</button>
        </div>
        <div v-else>
          <button v-if="isInitialGame" @click="startNewGame" class="success">
            New game
          </button>
          <button v-else @click="start" class="success">
            Continue<br><small>(space)</small>
          </button>
        </div>
      </div>
      <div class="ground__player" v-show="player.figureType && isStarted">
        <item class="ground__item" :figure="player.figureType" :weight="player.figureWeight"
          :style="playerPosition" />
      </div>
      <div class="ground__computer" v-show="computer.figureType && isStarted">
        <item class="ground__item" :figure="computer.figureType" :weight="computer.figureWeight"
          :style="computerPosition" />
      </div>
    </div>
    <libra :rotate="rotate" :map-items="mapItems" :score-game="scoreGame" :is-show="isLose" />
    <div class="" style="text-align: center;">
      <p>
        <button @click="toggleStart">Space<br><small>Start - Pause</small></button>
      </p>
      <p>
        <button @click="startNewGame">Enter<br><small>New Game</small></button>
      </p>
      <p>
        <button @click="moveLeft">Left</button>
        -
        <button @click="moveRight">Right</button><br>
        <small>Player item move</small>
      </p>
    </div>


  </div>
</template>

<script>
import { mapActions, mapState, mapGetters } from 'vuex';
import {
  ITEM_SIZE, MAX_CHANGE, MAP_SIZE, MAP_LEVELS,
} from '../settings';
import Item from './Item.vue';
import Libra from './Libra.vue';

export default {
  name: 'Main',
  data() {
    return {
      isInitialGame: true,
      isLose: false,
    };
  },
  components: {
    Libra,
    Item,
  },
  computed: {
    playerPosition() {
      return {
        left: `${this.player.position * ITEM_SIZE}px`,
        top: `${this.level * ITEM_SIZE}px`,
      };
    },
    computerPosition() {
      return {
        left: `${this.computer.position * ITEM_SIZE}px`,
        top: `${this.level * ITEM_SIZE}px`,
      };
    },
    battleGroundStyle() {
      return {
        'background-size': `${ITEM_SIZE}px ${ITEM_SIZE}px`,
        height: `${(MAP_LEVELS + 1) * ITEM_SIZE}px`,
      };
    },
    mainStyle() {
      return {
        'max-width': `${((MAP_SIZE + 1) * 2) * (ITEM_SIZE) + 1}px`,

      };
    },
    ...mapState({
      player: state => state.battleGround.player,
      computer: state => state.battleGround.computer,
      level: state => state.battleGround.level,
      isStarted: state => !!state.timeoutId,
    }),
    ...mapGetters({
      rotate: 'rotate',
      mapItems: 'mapItems',
      scoreGame: 'scoreGame',
    }),
  },
  created() {
    document.addEventListener('keydown', (event) => {
      if (this.isLose || this.isInitialGame) {
        switch (event.code) {
          case 'Space' && 'Enter':
            this.startNewGame();
            break;
        }
        return;
      }
      // eslint-disable-next-line default-case
      switch (event.code) {
        case 'ArrowLeft':
          this.moveLeft();
          break;
        case 'ArrowRight':
          this.moveRight();
          break;
        case 'Space':
          this.toggleStart();
          break;
        case 'ArrowDown':
          this.start();
          break;
        case 'Enter':
          this.startNewGame();
          break;
      }
    });
  },
  methods: {
    toggleStart() {
      if (this.isStarted) {
        this.stop();
      } else {
        this.start();
      }
    },
    startNewGame() {
      this.isLose = false;
      this.isInitialGame = false;
      this.newGame();
      this.start();
    },
    ...mapActions(['moveLeft', 'moveRight', 'start', 'stop', 'newGame']),
  },
  watch: {
    rotate(value) {
      if (value > MAX_CHANGE || value < -MAX_CHANGE) {
        this.isLose = true;
        this.stop();
      }
    },
  },
};
</script>

<style scoped lang="scss">
.main {
  margin: 0 auto;
}

.title {
  margin: 0;
}

.ground {
  position: relative;
  background: linear-gradient(#1f1f1f, transparent 2px),
    linear-gradient(90deg, #1f1f1f, transparent 2px);
    background-color: #0000004d;

  .ground__item {
    position: absolute;
    z-index: 2;
  }

  .ground__player {
    position: absolute;
    width: 50%;
    height: 100%;
  }

  .ground__computer {
    position: absolute;
    width: 50%;
    height: 100%;
    left: 50%;
  }
}

.start-view {
  height: 100%;
  background: #4e4e4ebd;
  display: flex;
  font-size: 1.5rem;
  align-items: center;
  justify-content: center;
}

.level__info {
  background: rgba(255, 0, 0, 0.747);
  text-align: center;
  padding: 20px;
  border-radius: 15px;
  color: white;
  z-index: 1;
}
</style>
