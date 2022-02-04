<template>
  <div class="tracking-window">
    <div class="target" :style="styles" @transitionend="onBoxTransitionEnd" @mousemove="$emit('score', 1 * difficulty)" />
  </div> 
</template>

<script>
export default {
  data: () => ({
    speed: 150,
    boxSize: 50,
    windowWidth: 0,
    windowHeight: 0,
    boxNextPosition: [0, 0],
    boxNextPositionTravelTime: 0
  }),

  props: {
    isDifficultyChosen:{
      type: Boolean
    },
    countdownTimer:{
      default: null
    },
    timeRemaining:{
      default: null
    },
    difficulty:{
      type: Number,
      default: 1
    }
  },

  computed: {
    styles() {
      const [x, y] = this.boxNextPosition;
      const size = `width: ${this.boxSize}px; height: ${this.boxSize}px;`;
      const transform = `transform: translate3d(${x}px, ${y}px, 0);`;
      const transition = `transition: transform ${this.boxNextPositionTravelTime}s linear;`;
      return `${size} ${transform} ${transition}`;
    },
  },

  mounted() {
    this.getWindowDimensions();
    window.addEventListener("resize", this.onWindowResize);

    requestAnimationFrame(this.sendBoxToRandomPoint);
    switch(this.difficulty) {
      case 1:  
        this.boxSize = 70
        this.speed = 250
        break
      case 2: 
        this.boxSize = 50
        this.speed = 350
        break
      case 3:  
        this.boxSize = 50
        this.speed = 450
        break
      case 4:  
        this.boxSize = 50
        this.speed = 650
        break
    }
  },

  beforeDestroy() {
    window.removeEventListener("resize", this.onWindowResize);
  },

  methods: {
    sendBoxToRandomPoint() {
      const nextPoint = this.getRandomBoxPosition();
      const pathLenght = this.getDistanceBetweenPoints(
        this.boxNextPosition,
        nextPoint
      );
      this.boxNextPosition = nextPoint;
      this.boxNextPositionTravelTime = pathLenght / this.speed;
    },

    onWindowResize() {
      this.getWindowDimensions();
    },

    getWindowDimensions() {
      this.windowHeight = 850;
      this.windowWidth = 1900;

    },

    getRandomBoxPosition() {
      const maxX = this.windowWidth - this.boxSize;
      const maxY = this.windowHeight - this.boxSize;
      return [
        Math.floor(Math.random() * maxX),
        Math.floor(Math.random() * maxY),
      ];
    },

    getDistanceBetweenPoints([xa, ya], [xb, yb]) {
      const dx = xa - xb;
      const dy = ya - yb;
      return Math.sqrt(dx ** 2 + dy ** 2);
    },

    onBoxTransitionEnd() {
      this.sendBoxToRandomPoint();
    },

    
  },
};
</script>

<style>
.tracking-window {
  background: aliceblue;
  height: 900px;
  width: 100vw;
}

.target {
  background: red;
}

.visible {
  display: block;
}
</style>
