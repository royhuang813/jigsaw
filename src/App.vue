<template>
  <div id="app">
    <h1 class="game-title">拼图游戏</h1>
    <p class="pass-text" v-show="isPass">恭喜通关!</p>
    <ul class="box" :style="{ width: size * 100 + 'px' }">
      <li
        class="item"
        v-for="(item, index) in imgsArr"
        :key="index"
        :data-x="item.x"
        :data-y="item.y"
        :data-id="item.id"
      >
        {{ item.name }}
      </li>
    </ul>
    <ul class="panel">
      <li>
        <button class="start-btn" @click="startGame">开始游戏</button
        ><span class="spend-time">耗时：{{ time }}s</span>
      </li>
      <li>
        <span>当前宫格：{{ size }}</span
        ><input
          type="range"
          name=""
          id=""
          :value="size"
          @input="handleInputSize"
          min="3"
          max="7"
          step="1"
        />
      </li>
      <li>已走步数：{{ step }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      size: 3, //box size, like 3*3 4*4 ...   default is 3*3
      imgsArr: [
        // { x: 1, y: 3, id: 1, name: 1 },
        // { x: 2, y: 3, id: 2, name: 2 },
        // { x: 3, y: 3, id: 3, name: 3 },
        // { x: 1, y: 2, id: 4, name: 4 },
        // { x: 2, y: 2, id: 5, name: 5 },
        // { x: 3, y: 2, id: 6, name: 6 },
        // { x: 1, y: 1, id: 7, name: 7 },
        // { x: 2, y: 1, id: 8, name: 8 },
        // { x: 3, y: 1, id: 9, name: "X" },
      ],
      curSpaceXAxis: Number, //the space item current status
      curSpaceYAxis: Number,
      curSpaceIndex: Number,
      step: 0, //record step
      isPass: false, //game pass or not
      time: 0, //use time
      isStart: false, //game start or not
      timer: null,
    };
  },
  mounted() {
    this.buildBox(this.size);
    this.moveListen();
  },
  methods: {
    isVictory() {
      for (let i = 0, l = this.imgsArr.length; i < l; i++) {
        if (this.imgsArr[i]["id"] !== i) {
          return false;
        }
      }
      return true;
    },
    changeSet(target, space) {
      [this.imgsArr[space], this.imgsArr[target]] = [
        this.imgsArr[target],
        this.imgsArr[space],
      ];
      this.curSpaceIndex = target;
      this.step++;
      this.$forceUpdate();
      if (this.isVictory()) {
        this.isPass = true;
        clearInterval(this.timer);
      }
    },
    findWhoWillBeMoved(axis, direction) {
      //left or right
      if (axis === "x") {
        if (direction === "left" && this.curSpaceXAxis + 1 <= this.size) {
          let targetIndex = Number; //the item index who will be moved
          for (let i = 0, l = this.imgsArr.length; i < l; i++) {
            if (
              this.imgsArr[i]["x"] === this.curSpaceXAxis + 1 &&
              this.imgsArr[i]["y"] === this.curSpaceYAxis
            ) {
              targetIndex = i;
              this.imgsArr[i]["x"] = this.curSpaceXAxis;
              this.imgsArr[this.curSpaceIndex]["x"] = this.curSpaceXAxis + 1;
              this.curSpaceXAxis = this.curSpaceXAxis + 1;
              break;
            }
          }
          this.changeSet(targetIndex, this.curSpaceIndex);
        } else if (direction === "right" && this.curSpaceXAxis - 1 > 0) {
          let targetIndex = Number; //the item index who will be moved
          for (let i = 0, l = this.imgsArr.length; i < l; i++) {
            if (
              this.imgsArr[i]["x"] === this.curSpaceXAxis - 1 &&
              this.imgsArr[i]["y"] === this.curSpaceYAxis
            ) {
              targetIndex = i;
              this.imgsArr[i]["x"] = this.curSpaceXAxis;
              this.imgsArr[this.curSpaceIndex]["x"] = this.curSpaceXAxis - 1;
              this.curSpaceXAxis = this.curSpaceXAxis - 1;
              break;
            }
          }
          this.changeSet(targetIndex, this.curSpaceIndex);
        }
      } else if (axis === "y") {
        if (direction === "up" && this.curSpaceYAxis - 1 > 0) {
          let targetIndex = Number; //the item index who will be moved
          for (let i = 0, l = this.imgsArr.length; i < l; i++) {
            if (
              this.imgsArr[i]["x"] === this.curSpaceXAxis &&
              this.imgsArr[i]["y"] === this.curSpaceYAxis - 1
            ) {
              targetIndex = i;
              this.imgsArr[i]["y"] = this.curSpaceYAxis;
              this.imgsArr[this.curSpaceIndex]["y"] = this.curSpaceYAxis - 1;
              this.curSpaceYAxis = this.curSpaceYAxis - 1;
              break;
            }
          }
          this.changeSet(targetIndex, this.curSpaceIndex);
        } else if (
          direction === "down" &&
          this.curSpaceYAxis + 1 <= this.size
        ) {
          let targetIndex = Number; //the item index who will be moved
          for (let i = 0, l = this.imgsArr.length; i < l; i++) {
            if (
              this.imgsArr[i]["x"] === this.curSpaceXAxis &&
              this.imgsArr[i]["y"] === this.curSpaceYAxis + 1
            ) {
              targetIndex = i;
              this.imgsArr[i]["y"] = this.curSpaceYAxis;
              this.imgsArr[this.curSpaceIndex]["y"] = this.curSpaceYAxis + 1;
              this.curSpaceYAxis = this.curSpaceYAxis + 1;
              break;
            }
          }
          this.changeSet(targetIndex, this.curSpaceIndex);
        }
      }
    },
    moveListen() {
      document.onkeyup = (e) => {
        if (this.isStart) {
          switch (e.keyCode) {
            case 37: {
              //left
              this.findWhoWillBeMoved("x", "left");
              break;
            }
            case 38: {
              //up
              this.findWhoWillBeMoved("y", "up");
              break;
            }
            case 39: {
              //right
              this.findWhoWillBeMoved("x", "right");
              break;
            }
            case 40: {
              //down
              this.findWhoWillBeMoved("y", "down");
              break;
            }
          }
        }
      };
    },
    startGame() {
      this.isStart = true;
      this.timer = setInterval(() => {
        this.time++;
      }, 1000);
    },
    handleInputSize(e) {
      let val = e.target.value;
      this.buildBox(e.target.value * 1);
      this.size = val;
    },
    buildBox(size) {
      let num = 0;
      let imgsArr = [];
      let tempSize = size;
      let total = size ** 2;
      // for (let i = size; i > 0; i--) {
      //   for (let j = 1; j <= size; j++) {
      //     let obj = {
      //       x: j,
      //       y: i,
      //       id: num,
      //       name: num,
      //     };
      //     imgsArr.push(obj);
      //     num++;
      //   }
      // }

      // build item
      while (total > 0) {
        imgsArr.push({ id: num, name: num + 1 });
        num++;
        total--;
      }
      let randomImgsArr = this._.shuffle(imgsArr);
      let tempImgsArr = [];
      // build axis info
      for (let i = tempSize; i > 0; i--) {
        for (let j = 1; j <= tempSize; j++) {
          let obj = {
            x: j,
            y: i,
          };
          tempImgsArr.push(obj);
        }
      }
      //get space item
      for (let i = 0, l = tempImgsArr.length; i < l; i++) {
        randomImgsArr[i] = Object.assign(randomImgsArr[i], tempImgsArr[i]);
        if (randomImgsArr[i]["id"] === tempSize ** 2 - 1) {
          randomImgsArr[i]["name"] = "";
          const { x, y } = randomImgsArr[i];
          this.curSpaceXAxis = x;
          this.curSpaceYAxis = y;
          this.curSpaceIndex = i;
        }
      }
      // console.log("q", randomImgsArr);
      // Object.assign(randomImgsArr[randomImgsArr.length - 1], { name: " " });
      // const { x, y } = randomImgsArr[randomImgsArr.length - 1];
      // this.curSpaceXAxis = x;
      // this.curSpaceYAxis = y;
      // this.curSpaceIndex = randomImgsArr.length - 1;
      this.imgsArr = randomImgsArr;
      this.$forceUpdate();
    },
  },
};
</script>

<style lang="scss">
* {
  background-color: antiquewhite;
}
#app {
  position: relative;
}
.game-title {
  color: #333;
  text-align: center;
}
.pass-text {
  padding: 20px 0 0 0;
  color: rgb(22, 196, 22);
  font-size: 20px;
  text-align: center;
}
.box {
  display: flex;
  flex-wrap: wrap;
  margin: 50px auto 0;
  color: red;
  outline: 1px solid #000;
  .item {
    width: 100px;
    height: 100px;
    text-align: center;
    line-height: 100px;
    outline: 1px solid #fff;
  }
}
.panel {
  position: absolute;
  top: 10px;
  right: 200px;
  .start-btn {
    margin: 0 0 10px 0;
    &:hover {
      color: green;
    }
    &:active {
      color: green;
      // outline: none;
    }
    &:focus {
      outline: none;
    }
  }
  .spend-time {
    padding: 0 0 0 50px;
  }
}
</style>

