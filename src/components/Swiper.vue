<template>
  <div class="hy-swiper">
    <div class="swiper" @touchstart="touchStart" @touchmove="touchMove" @touchend="touchEnd">
      <swiper-item v-for="(item, index) in pictures" :key="index" :class="{focus: currentIndex === index}">
        <img :src="item" alt="" class="image">
      </swiper-item>
    </div>
    <div class="indi-bar">
      <div class="indicator" 
        :class="{active: currentIndex - 1 === index}" 
        v-for="(item, index) in pictures.length" 
        :key="index"
        @click="indicatorClick(index)">
      </div>
    </div>
  </div>
</template>

<script>
import pic1 from "../assets/capsule1.jpg"
import pic2 from "../assets/capsule2.jpg"
import pic3 from "../assets/capsule3.jpg"
import pic4 from "../assets/capsule4.jpg"
import SwiperItem from './SwiperItem.vue'
export default {
  components: { SwiperItem },
  name: "Swiper",
  created() {},
  data() {
    return {
      pictures: [
        pic1, pic2, pic3, pic4
      ],
      swiperStyle: {},
      imageWidth: 0,
      currentIndex: 1,
      duration: 2000,
      len: 0,
      timer: null,
      isScrolling: true,
      currentX: 0,
      distance: 0,
      startX: 0,
      moveRatio: 0.3,
      animation: 300
    };
  },
  mounted() {
    setTimeout(() => {
      this.handleDOM()
      this.startTimer()
    }, 200);
  },
  methods: {
    handleDOM() {
      let swiper = document.querySelector('.swiper')
      let slide = document.getElementsByClassName('slide')
      this.len = this.pictures.length
      if (this.len > 1) {
        let cloneFirst = slide[0].cloneNode(true)
        let cloneLast = slide[this.len - 1].cloneNode(true)
        swiper.insertBefore(cloneLast, slide[0])
        swiper.appendChild(cloneFirst)
        this.imageWidth = swiper.offsetWidth
        // console.log(this.imageWidth);
        this.swiperStyle = swiper.style
        // console.log(swiper);
        // console.log(this.swiperStyle);
      }
      this.setTransform(-this.imageWidth)
    },
    setTransform(position) {
      this.swiperStyle.transform = `translate3d(${position}px, 0, 0)`
      // this.swiperStyle['-webkit-transform'] = `translate3d(${position}px), 0, 0`;
      // this.swiperStyle['-ms-transform'] = `translate3d(${position}px), 0, 0`;
    },
    startTimer() {
      this.timer = window.setInterval(() => {
        this.currentIndex++
        this.scrollSwiper(-this.currentIndex * this.imageWidth)
        // console.log(this.currentIndex);
      }, this.duration)
    },
    stopTimer() {
      window.clearInterval(this.timer)
    },
    scrollSwiper(position) {
      this.isScrolling = true
      this.swiperStyle.transition = 'transform ' + this.animation + 'ms'
      this.setTransform(position)
      this.checkPosition()
      this.isScrolling = false
    },
    checkPosition() {
      setTimeout(() => {
        this.swiperStyle.transition = '0ms'
        if (this.currentIndex >= this.len + 1) {
          this.currentIndex = 1
          this.setTransform(-this.currentIndex * this.imageWidth)
        } else if (this.currentIndex <= 0) {
          this.currentIndex = this.len
          this.setTransform(-this.currentIndex * this.imageWidth)
        }
      }, this.animation);
    },
    touchStart(evt) {
      // console.log(evt);
      if (this.isScrolling) return
      this.stopTimer()
      this.startX = evt.touches[0].pageX
    },
    touchMove(evt) {
      // console.log(evt);
      this.currentX = evt.touches[0].pageX
      this.distance = this.currentX - this.startX
      const currentPositon = -this.currentIndex * this.imageWidth
      const moveDistance = currentPositon + this.distance
      this.setTransform(moveDistance)
    },
    touchEnd() {
      // console.log(evt);
      let moveDistance = Math.abs(this.distance)
      if (this.distance === 0) {
        return
      } else if (this.distance > 0 && moveDistance > this.imageWidth * this.moveRatio) {
        this.currentIndex--
      } else if (this.distance < 0 && moveDistance > this.imageWidth * this.moveRatio) {
        this.currentIndex++
      }
      this.scrollSwiper(-this.currentIndex * this.imageWidth)
      this.startTimer()
    },
    indicatorClick(index) {
      this.currentIndex = index + 1
      this.stopTimer()
      this.startTimer()
    }
  },
};
</script>

<style lang="css" scoped>
.hy-swiper {
  width: 400px;
  margin: 0 auto;
  overflow: hidden;
}
.swiper {
  display: flex;
}
.image {
  width: 400px;
}
.indi-bar {
  display: flex;
  position: relative;
  top: -40px;
  margin: 0 auto;
  width: 30%;
}
.indicator {
  top: 100px;
  height: 10px;
  width: 10px;
  border-radius: 10px;
  background-color: #fff;
  margin: 0 auto;
}
.active {
  background-color: red;
}
</style>