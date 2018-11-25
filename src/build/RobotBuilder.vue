<template>
    <div class="content">
        <button class="add-to-cart" :style="counter ? styleCounter : null" @click="addToCart">Add to Cart</button>
        <button class="reset-cart" @click="resetCart">Reset Cart</button>
        <div class="top-row">
            <part-selector
                :parts="availableParts.heads"
                position="top"
                @selectPart="selectPart"/>
        </div>
        <div class="middle-row">
            <part-selector
                :parts="availableParts.arms"
                position="left"
                @selectPart="selectPart"/>
            <part-selector
                :parts="availableParts.torsos"
                position="center"
                @selectPart="selectPart"/>
            <part-selector
                :parts="availableParts.arms"
                position="right"
                @selectPart="selectPart"/>
        </div>
        <div class="bottom-row">
            <part-selector
                :parts="availableParts.bases"
                position="bottom"
                @selectPart="selectPart"/>
        </div>
        <div>
            <h1>Cart</h1>
            <table>
                <thead v-if="counter">
                <tr>
                    <th>Robot</th>
                    <th class="cost">Cost</th>
                </tr>
                </thead>
                <tbody>
                    <tr :id="'cart' + index" v-for="(robot, index) in cart" :key="index" @click="removeRow(index)">
                        <td>{{robot.head.title}}</td>
                        <td class="cost">{{robot.cost}}</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
import availableParts from '../data/parts';
import createdHookMixin from './created-hook-mixin';
import PartSelector from './PartSelector';

export default {
  name: 'RobotBuilder',
  components: {PartSelector},
  data() {
      return {
          availableParts,
          cart: [],
          selectedRobot: {
              head: {},
              leftArm: {},
              rightArm: {},
              torso: {},
              base: {},
              cost: {},
          },
          counter: 0,
      };
  },
  mixins: [createdHookMixin],
  computed: {
      saleOrderClass() {
          return this.selectedRobot.head.onSale ? 'sale-border' : '';
          },
      styleCounter() {
          return {
              '--content': '"' + this.counter + '"',
              '--display': 'inline-block'
          };
      }
  },
  methods: {
      addToCart() {
          const robot = this.selectedRobot;
          const cost = robot.head.cost +
                       robot.leftArm.cost +
                       robot.torso.cost +
                       robot.rightArm.cost +
                       robot.base.cost;
          this.cart.push(Object.assign({}, robot, { cost }));

          this.counter++;
      },
      resetCart() {
          this.cart = [];
          this.counter = 0;
      },
      selectPart(part, position) {
          let arms = ["left", "right"];
          if (arms.includes(position)) {
              this.selectedRobot[position + part.type.charAt(0).toUpperCase() + part.type.slice(1)] = part;

              return;
          }

          this.selectedRobot[part.type] = part;
      },
      removeRow(index) {
          let row = document.getElementById('cart' + index);
          row.style.display = "none";
          this.counter--;
      }
  },
};
</script>

<style lang="scss" scoped>
    .part {
        position: relative;
        width:165px;
        height:165px;
        border: 3px solid #aaa;
    }
    .part {
        img {
            width:165px;
        }
    }
    .top-row {
        display:flex;
        justify-content: space-around;
    }
    .middle-row {
        display:flex;
        justify-content: center;
    }
    .bottom-row {
        display:flex;
        justify-content: space-around;
        border-top: none;
    }
    .head {
        border-bottom: none;
    }
    .left {
        border-right: none;
    }
    .right {
        border-left: none;
    }
    .left img {
        transform: rotate(-90deg);
    }
    .right img {
        transform: rotate(90deg);
    }
    .bottom {
        border-top: none;
    }
    .prev-selector {
        position: absolute;
        z-index:1;
        top: -3px;
        left: -28px;
        width: 25px;
        height: 171px;
        border: none;
    }
    .next-selector {
        position: absolute;
        z-index:1;
        top: -3px;
        right: -28px;
        width: 25px;
        height: 171px;
        border: none;
    }
    .center .prev-selector, .center .next-selector {
        opacity:0.8;
    }
    .left .prev-selector {
        top: -28px;
        left: -3px;
        width: 144px;
        height: 25px;
    }
    .left .next-selector {
        top: auto;
        bottom: -28px;
        left: -3px;
        width: 144px;
        height: 25px;
    }
    .right .prev-selector {
        top: -28px;
        left: 24px;
        width: 144px;
        height: 25px;
    }
    .right .next-selector {
        top: auto;
        bottom: -28px;
        left: 24px;
        width: 144px;
        height: 25px;
    }
    .right .next-selector {
        right: -3px;
    }
    .robot-name {
        position: absolute;
        top: -25px;
        text-align: center;
        width: 100%
    }
    .sale {
        color: red;
    }
    .content {
        position: relative;
    }
    .add-to-cart {
        position: absolute;
        right: 150px;
        width: 110px;
        padding: 3px;
        font-size: 16px;
        --content: "";
        --display: none;

        &::after {
            display: var(--display);
            content: var(--content);
            color: red;
            margin-left: 7px;
            position: absolute;
            top: -24px;
            right: -10px;
            background-color: white;
            border-radius: 50%;
            padding: 4px;
            border: 1px solid lightgray;
            height: 16px;
            width: 16px;
            transform: scale(1.2);
        }
    }
    .reset-cart {
        position: absolute;
        right: 5px;
        width: 110px;
        padding: 3px;
        font-size: 16px;
    }
    table {
        border-collapse: collapse;
    }
    thead {
        tr::after {
            content: "";
        }
    }
    td, th {
        text-align: left;
        padding: 5px 20px 5px 5px;
    }
    tr {
        border: 1px solid black;
        pointer-events: none;
        &::after {
            content: "\00d7";
            margin-right: 5px;
            pointer-events: all;
        }
    }
    tr:hover {
        cursor: pointer;
    }
    .cost {
        text-align: right;
    }
    .sale-border {
        border: 3px solid red;
    }
    .active {
        display: none;
    }
</style>
