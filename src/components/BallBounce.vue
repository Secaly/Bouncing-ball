<template>
  <div id="ball-container" class="container">
    <div
      class="ball"
      :style="{
        top: ballY + 'px',
        left: ballX + 'px',
        height: size + 'px',
        width: size + 'px',
      }"
    ></div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  data() {
    return {
      size: 80,
      ballX: 0,
      ballY: 0,
      time: 0,
      isDragging: false,
      BOUNCE: 0.7,
      VELOCITY: 0.2,
      interval: setInterval(this.updateBall, 16),
    };
  },
  methods: {
    updateBall() {
      // the ball gains velocity as it falls
      this.time += this.VELOCITY;
      // if the ball does not touch the bottom of the screen, update its position
      // else, reverse its speed by lowering it at each bounce
      if (this.ballY < window.innerHeight - this.size - this.time) {
        this.ballY += this.time;
      } else {
        this.time *= -1 * this.BOUNCE;
        // put the ball back at the bottom of the screen to simulate the contact with the ground
        this.ballY = window.innerHeight - this.size;
        // stop the interval if there is no need to calculate the fall of the ball
        if (this.time > -0.08) {
          clearInterval(this.interval);
        }
      }
    },
    dragBall(event: MouseEvent) {
      // centers the ball on the mouse and sets its fall time to 0
      this.time = 0;
      this.ballX = event.clientX - this.size / 2;
      this.ballY = event.clientY - this.size / 2;
    },
    handleResize() {
      // reset the ball data in case of window resize
      this.time = 0;
      this.ballX = 0;
      this.ballY = 0;
      clearInterval(this.interval);
      this.interval = setInterval(this.updateBall, 16);
    },
    handleMouseDown(event: MouseEvent) {
      // reposition the ball on the mouse and remove the drop calculator
      this.isDragging = true;
      clearInterval(this.interval);
      this.dragBall(event);
      // prevents repositioning of the ball outside the screen
      if (event.clientY > window.innerHeight - this.size / 2) {
        this.ballY = window.innerHeight - this.size;
      } else if (event.clientY < this.size / 2) {
        this.ballY = 0;
      }
      if (event.clientX > window.innerWidth - this.size / 2) {
        this.ballX = window.innerWidth - this.size;
      } else if (event.clientX < this.size / 2) {
        this.ballX = 0;
      }
    },
    handleMouseUp() {
      // launches the calculation of the fall
      this.isDragging = false;
      this.interval = setInterval(this.updateBall, 16);
    },
    handleMouseMove(event: MouseEvent) {
      // updates the mouse position and prevents it from being moved off the screen
      if (
        this.isDragging &&
        event.clientY < window.innerHeight - this.size / 2 &&
        event.clientY > this.size / 2 &&
        event.clientX < window.innerWidth - this.size / 2 &&
        event.clientX > this.size / 2
      ) {
        this.dragBall(event);
      }
    },
  },
  mounted() {
    window.addEventListener('mousedown', this.handleMouseDown);
    window.addEventListener('mouseup', this.handleMouseUp);
    window.addEventListener('mousemove', this.handleMouseMove);
    window.addEventListener('resize', this.handleResize);
  },
  unmounted() {
    clearInterval(this.interval);
    window.removeEventListener('resize', this.handleResize);
    window.removeEventListener('mousemove', this.handleMouseDown);
    window.removeEventListener('mousemove', this.handleMouseUp);
    window.removeEventListener('mousemove', this.handleMouseMove);
  },
});
</script>

<style lang="scss">
.container {
  display: flex;
  align-items: center;
  justify-content: center;
  background: black;
  min-width: 320px;
  min-height: 480px;
  width: 100vw;
  height: 100vh;
}

.ball {
  background-image: url('https://www.le-geant-de-la-fete.com/4910-large_default/ballon-de-plage-41-cm.jpg');
  background-size: cover;
  border-radius: 50%;
  position: absolute;
}
</style>
