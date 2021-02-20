<template>
  <div id="app">
    <ul class="box" :style="{ width: size * 100 + 'px' }">
      <li
        class="item"
        v-for="(item, index) in imgsArr"
        :key="index"
        :data-x="item.x"
        :data-y="item.y"
        :data-id="item.id - 1"
      >
        {{ item.name }}
      </li>
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
    };
  },
  mounted() {
    this.buildBox(this.size ** 1);
    this.moveListen();
  },
  methods: {
    changeSet(target, space) {
      [this.imgsArr[space], this.imgsArr[target]] = [
        this.imgsArr[target],
        this.imgsArr[space],
      ];
      this.curSpaceIndex = target;
      this.$forceUpdate();
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
        if (direction === "minus") {
        } else if (direction === "add") {
        }
      }
    },
    moveListen() {
      document.onkeyup = (e) => {
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
      };
    },
    buildBox(size) {
      let num = 1;
      let imgsArr = [];
      for (let i = size; i > 0; i--) {
        for (let j = 1; j <= size; j++) {
          let obj = {
            x: j,
            y: i,
            id: num,
            name: num,
          };
          imgsArr.push(obj);
          num++;
        }
      }
      Object.assign(imgsArr[imgsArr.length - 1], { name: " " });
      const { x, y } = imgsArr[imgsArr.length - 1];
      this.curSpaceXAxis = x;
      this.curSpaceYAxis = y;
      this.curSpaceIndex = imgsArr.length - 1;
      this.imgsArr = imgsArr;
    },
  },
};
</script>

<style lang="scss">
* {
  background-color: antiquewhite;
}
.box {
  display: flex;
  flex-wrap: wrap;
  margin: 100px auto;
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
</style>

