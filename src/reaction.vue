<template>
    <div class="wrapper">
        <div class="targets">
            <div class="target" :delay="delay(random(1,5),timeRandom(1000,4000))" 
                                :class="{ visible:isVisible }" 
                                v-for="target in targets" 
                                :key="target.index">{{target}}</div>
        </div>
    </div>
</template>

<script>
export default {
props: {
    timeRemaining:{
        default: '30'
    },
    score:{
        type: Number
    },
    isGamePlaying:{
        type: Boolean
    },
},

data() {
  return {
      targets:[1,2,3,4,5],
      isVisible: false,
      delay: 2000
  }
},

methods: {
    random(min, max) {
        let random = min + Math.random() * (max + 1 - min);
        return Math.floor(random);
    },
    timeRandom(min, max) {
        let random = min + Math.random() * (max + 100 - min);
        return Math.floor(random);
    },
    delay(f, ms) {
        return function() {
            setTimeout(() => f.apply(this, arguments), ms);
        };
    },
    spotRng (spotRandom, delayRandom) {
        let spot = spotRandom(1,5)
        let delay = delayRandom(1000,4000)
        return spot, delay
    },

},

computed: {
    
}
}
</script>

<style lang="css" scoped>
.wrapper {
    background: url(./assets/angle.jpg) no-repeat;
    background-size: 100% 92vh;
    margin-top: 100px;
}

.targets {
    height: 400px;
    z-index:240;
    width: 400px;
    margin-top: 410px;
    opacity: 0.9; 
    display: flex;
}

.target {
    height: 40px;
    width: 40px;
    background-color: aliceblue;
    z-index: 500;
    border-radius: 50%;
    margin: 0 5px 0 5px;
    display: none;
}

.visible {
    display: flex;
}

</style>