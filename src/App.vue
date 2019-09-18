<template>
  <div id="app">
    <div id="calculator-display">
      <div class="display-section" :style="{fontSize: fontSize + 'vw' }">{{showNumber}}</div>
    </div>
    <div id="calculator-buttons">
        <div
        v-for="(item, index) in buttons"
        :id="item.btnId"
        :key="item.btnId"
        class="calculator-button"
        :data-checked="'false'"
        @click="clickButton(index, $event)">{{item.btnText}}</div>
    </div>
  </div>
</template>

<script>
import Velocity from 'velocity-animate'
function Button (id, text, startColor, endColor, color) {
  this.btnId = id
  this.btnText = text
  this.btnStartColor = startColor
  this.btnEndColor = endColor
  this.btnColor = color
}
// 动画效果函数
Button.prototype.buttonAnimation = function () {
  document.getElementById(this.btnId).style.backgroundColor = this.btnStartColor
  Velocity(document.getElementById(this.btnId), {
    backgroundColor: this.btnEndColor,
    color: this.btnColor
  }, {
    duration: 500
  })
}

var oButton1 = new Button('clear', 'AC', '#d9d9d9', '#a6a6a6', '#000')
var oButton2 = new Button('pn', '+/-', '#d9d9d9', '#a6a6a6', '#000')
var oButton3 = new Button('percent', '%', '#d9d9d9', '#a6a6a6', '#000')
var oButton4 = new Button('divisor', '÷', '#fdd4a8', '#ffffff', '#ff9501')
var oButton5 = new Button('seven', '7', '#737373', '#333333', '#fff')
var oButton6 = new Button('eight', '8', '#737373', '#333333', '#fff')
var oButton7 = new Button('nine', '9', '#737373', '#333333', '#fff')
var oButton8 = new Button('multiplication', '×', '#fdd4a8', '#ffffff', '#ff9501')
var oButton9 = new Button('four', '4', '#737373', '#333333', '#fff')
var oButton10 = new Button('five', '5', '#737373', '#333333', '#fff')
var oButton11 = new Button('six', '6', '#737373', '#333333', '#fff')
var oButton12 = new Button('minus', '-', '#fdd4a8', '#ffffff', '#ff9501')
var oButton13 = new Button('one', '1', '#737373', '#333333', '#fff')
var oButton14 = new Button('two', '2', '#737373', '#333333', '#fff')
var oButton15 = new Button('three', '3', '#737373', '#333333', '#fff')
var oButton16 = new Button('plus', '+', '#fdd4a8', '#ffffff', '#ff9501')
var oButton17 = new Button('zero', '0', '#737373', '#333333', '#fff')
var oButton18 = new Button('point', '.', '#737373', '#333333', '#fff')
var oButton19 = new Button('equal', '=', '#fdd4a8', '#ff9501', '#fff')

export default {
  name: 'app',
  data () {
    return {
      fontSize: 25,
      btnOperatorChecked: '',
      buttons: [
        oButton1,
        oButton2,
        oButton3,
        oButton4,
        oButton5,
        oButton6,
        oButton7,
        oButton8,
        oButton9,
        oButton10,
        oButton11,
        oButton12,
        oButton13,
        oButton14,
        oButton15,
        oButton16,
        oButton17,
        oButton18,
        oButton19
      ],
      showNumberArr: ['0'],
      numArr1: ['0'],
      numArr2: ['0'],
      numArr3: ['0'],
      operator1: '',
      operator2: ''
    }
  },
  computed: {
    showNumber: function () {
      return this.showNumberArr.join('')
    },
    operatorLevel1: function () {
      if (this.operator1 === '') {
        return 0
      } else {
        return (this.operator1 === 'plus' || this.operator1 === 'minus') ? 1 : 2
      }
    },
    operatorLevel2: function () {
      if (this.operator2 === '') {
        return 0
      } else {
        return (this.operator2 === 'plus' || this.operator2 === 'minus') ? 1 : 2
      }
    },
    showNumberLength: function () {
      let fontArr = [...this.showNumberArr]
      fontArr[0] === '-' && fontArr.shift()
      fontArr.includes('.') && fontArr.splice(fontArr.indexOf('.'), 1)
      return fontArr.length
    }
  },
  watch: {
    showNumberLength: function (val, oldVal) {
      if (val < 7) {
        this.fontSize = 25
      } else if (val === 7) {
        this.fontSize = 21.5
      } else if (val === 8) {
        this.fontSize = 19
      } else if (val === 9) {
        this.fontSize = 17
      } else {
        this.fontSize = 17
      }
    }
  },
  methods: {
    // 点击0~9按钮或者.按钮
    numberButton (index) {
      // 注意<>括号中的内容为点击的内容
      if (this.operator1 === '') {
        // 例：55<5>
        this.showNumberArr = this.numArr1
      } else if (this.operator1 !== '' && this.operator2 === '') {
        // 例：100+<5> or 100÷<5>
        this.showNumberArr = this.numArr2
      } else {
        // 例：+2×<5>; 9-6÷<3>
        this.showNumberArr = this.numArr3
      }

      if (index === 17) {
        // .按钮
        !this.showNumberArr.includes('.') && this.showNumberArr.push('.')
      } else if (index === 1) {
        // +/-按钮
        this.showNumberArr[0] === '-' ? this.showNumberArr.shift() : this.showNumberArr.unshift('-')
      } else if (index === 2) {
        // %按钮
        const nShowNumber = Number(this.showNumberArr.join(''))
        const nShowNumber2 = nShowNumber / 100
        const showNumber2Arr = String(nShowNumber2).split('')
        this.showNumberArr.splice(0, this.showNumberArr.length)
        for (let item of showNumber2Arr) {
          this.showNumberArr.push(item)
        }
      } else {
        if (this.showNumberArr[0] === '0' && this.showNumberArr.length === 1) {
          this.showNumberArr[0] = this.buttons[index].btnText
        } else {
          this.showNumberArr.push(this.buttons[index].btnText)
        }
      }
    },
    operatorButton (index) {
      // 注意<>括号中的内容为点击的内容
      // +、-、×、÷按钮
      if (this.operator1 === '') {
        // 例：1<+>
        this.operator1 = this.buttons[index].btnId
      } else if (this.operator1 !== '' && this.operator2 === '') {
        if (this.btnOperatorChecked !== '') {
          // 例：9-<+> or 9+<×>
          this.operator1 = this.buttons[index].btnId
        } else {
          // 例：9-1<+> or 9+8<×> or 9×8<×>，
          // 其中9-1<+>和9×8<×>按下后，马上计算前面的9-1，9×8 而9+8<×>不计算
          this.operator2 = this.buttons[index].btnId
          if (this.operatorLevel1 >= this.operatorLevel2) {
            this.equal1()
            this.operator1 = this.operator2
            this.operator2 = ''
            this.showNumberArr = this.numArr1
          }
        }
      } else {
        if (this.btnOperatorChecked !== '') {
          // 例：9-1×<+> or 9+8×<÷>
          this.operator2 = this.buttons[index].btnId
          if (this.operatorLevel1 === this.operatorLevel2) {
            this.equal1()
            this.operator1 = this.operator2
            this.operator2 = ''
            this.showNumberArr = this.numArr1
          }
        } else {
          // 例：9-2×3<+> or 9+8×2<÷>
          // 9-2×3<+> ==> 9-6<+> ==> 3<+>; 9+8×2<÷> ==> 9+16<÷>; 无论哪种，都先计算后面的
          this.equal2()
          this.operator2 = this.buttons[index].btnId
          if (this.operatorLevel1 === this.operatorLevel2) {
            this.equal1()
            this.operator1 = this.operator2
            this.operator2 = ''
            this.showNumberArr = this.numArr1
          } else {
            this.showNumberArr = this.numArr2
          }
        }
      }
    },
    equalButton () {
      // =按钮
      if (this.operator1 === '') {
      } else if (this.operator1 !== '' && this.operator2 === '') {
        // 6 - 3 (=); 6 + 3 (=); 6 × 3 (=); 6 ÷ 3 (=);
        this.btnOperatorChecked !== '' && (this.numArr2 = [...this.numArr1]) // 特殊情况: 6 + <=> ==> 6 + 6 <=> or 6 × <=>  ==> 6 × 6 <=>
        this.equal1()
        this.showNumberArr = this.numArr1
      } else {
        // 6 - 3 ÷ 1 (=);
        this.btnOperatorChecked !== '' && (this.numArr3 = [...this.numArr2]) // 特殊情况: 6 - 3 ÷ <=>  ==> 6 - 3 ÷ 3 <=>
        this.equal2()
        this.equal1()
        this.showNumberArr = this.numArr1
      }
    },
    equal1 () {
      const nNumArr1 = Number(this.numArr1.join(''))
      const nNumArr2 = Number(this.numArr2.join(''))
      let result
      if (this.operator1 === 'multiplication') {
        result = nNumArr1 * nNumArr2
      } else if (this.operator1 === 'divisor') {
        result = nNumArr1 / nNumArr2
      } else if (this.operator1 === 'plus') {
        result = nNumArr1 + nNumArr2
      } else if (this.operator1 === 'minus') {
        result = nNumArr1 - nNumArr2
      }
      this.numArr1 = String(result).split('')
      this.operator1 = ''
      this.numArr2 = ['0']
    },
    equal2 () {
      // 计算numArr2与numArr3 ；如2+3×5×，计算的是后面的3×5,并将值放入numArr2，其他numArr3=['0'], operator2=''
      const nNumArr2 = Number(this.numArr2.join(''))
      const nNumArr3 = Number(this.numArr3.join(''))
      let result
      if (this.operator2 === 'multiplication') {
        result = nNumArr2 * nNumArr3
      } else if (this.operator2 === 'divisor') {
        result = nNumArr2 / nNumArr3
      }
      this.numArr2 = String(result).split('')
      this.operator2 = ''
      this.numArr3 = ['0']
    },
    restoreAnimation () {
      // +、-、×、÷按钮恢复动画效果方法
      Velocity(document.getElementById(this.btnOperatorChecked), {
        backgroundColor: '#ff9501',
        color: '#ffffff'
      }, {
        duration: 500
      })
    },
    clickButton (index) {
      /* ——————————————————————计算逻辑—————————————————————— */
      if (index === 18) {
        this.equalButton(index)
      } else if ([3, 7, 11, 15].includes(index)) {
        this.operatorButton(index)
      } else if (index === 0) {
        this.btnOperatorChecked !== '' && this.restoreAnimation()
        this.btnOperatorChecked = ''
        this.showNumberArr = ['0']
        this.numArr1 = ['0']
        this.numArr2 = ['0']
        this.numArr3 = ['0']
        this.operator1 = ''
        this.operator2 = ''
      } else if ([1, 2, 17].includes(index)) {
        this.numberButton(index)
      } else {
        (this.showNumberLength < 9 || this.btnOperatorChecked !== '') && this.numberButton(index)
      }
      /* ——————————————————————按钮动画—————————————————————— */
      // 按钮点击时动画效果
      this.buttons[index].buttonAnimation()
      // +、-、×、÷按钮恢复动画效果
      if ([3, 7, 11, 15].includes(index)) {
        (this.btnOperatorChecked !== this.buttons[index].btnId && this.btnOperatorChecked !== '') && this.restoreAnimation()
        this.btnOperatorChecked = this.buttons[index].btnId
      } else if (index !== 1 && index !== 2) {
        this.btnOperatorChecked !== '' && this.restoreAnimation()
        this.btnOperatorChecked = ''
      }
    }
  }
}
</script>

<style lang="less">
* {
  margin: 0;
  padding: 0;
}
#app {
  font-family: Helvetica;
  background-color: #010101;
  height: 100vh;
  display: flex;
  flex-direction: column;
  .display-section {
    position: absolute;
    margin: auto 60px;
    bottom: 0;
    right: 0;
  }
}
#calculator-display {
  width: 100%;
  color: #fff;
  position: relative;
  flex-grow: 1;
  padding: 0 30px;
  font-size: 170px;
  box-sizing: border-box;
}
#calculator-buttons {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  padding: 30px;
  .calculator-button {
    width: 150px;
    height: 150px;
    margin:  15px 0;
    border-radius: 150px 150px;
    text-align: center;
    line-height: 150px;
    background-color: #ccc;
    font-size: 62px;
  }
  #zero {
    width: 330px;
  }
  #zero, #one, #two, #three, #four, #five, #six, #seven, #eight, #nine, #point {
    background-color: #333;
    color: #fff;
  }
  #clear, #pn, #percent {
    background: #a6a6a6;
    color: #000;
  }
  #divisor, #multiplication, #minus, #plus, #equal {
    background-color: #ff9501;
    color: #ffffff;
  }
}
</style>
