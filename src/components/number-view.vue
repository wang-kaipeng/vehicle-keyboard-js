<template>
  <div class="r-border vehicle-input-widget">
    <div class="input-area">
      <ul class="inputrow">
        <li class="cell"
          v-for="(text, index) in numberArray"
          :key="index"
          :class="['lengthof-' + numberArray.length, 
                    {'show-holder': showHolder(text, index,currentIndex)}]"
          :selected="(index === currentIndex)">
          <button class="key"
            :style="{'line-height': numberType !== undefined ? '1' : '180%'}"
            v-tap="{method: onCellSelected, params: { index }}">
            {{ handleHolder(text, index, currentIndex) }}
          </button>
        </li>
      </ul>
    </div>
    <div class="mode-switcher">
      <label class="mode--label">
        <input v-model="model"
          class="mode--radio"
          type="radio"
          value="1"
          @change="onModeChanged"
          name="mode-switcher">
        <span class="mode--radioInput" />
        <span class="mode--radioText">普通车牌</span>
      </label>
      <label class="mode--label">
        <input v-model="model"
          class="mode--radio"
          type="radio"
          value="2"
          @change="onModeChanged"
          name="mode-switcher">
        <span class="mode--radioInput" />
        <span class="mode--radioText">新能源车牌</span>
      </label>
    </div>
  </div>
</template>

<script>
import VueTap from './tap';
export default {
  directives: VueTap,
  name: 'number-view',
  props: {
    /**
     * 号码数组
     */
    numberArray: {
      type: Array,
      default: () => []
    },
    /**
     * 用于显示的车牌模式（0：自动探测，5：新能源）
     * @link{engine.NUM_TYPES}
     */
    numberType: Number,
    /**
     * 选中的号码索引
     */
    currentIndex: {
      type: Number,
      default: -1
    }
  },
  data() {
    return {
      model: '1'
    };
  },
  mounted() {
    try {
      document.querySelector(
        `.mode--radio[value="${this.model}"]`
      ).checked = true;
    } catch (error) {
      console.error(error);
    }
  },
  methods: {
    showHolder(text, index, currentIndex) {
      return (
        index === currentIndex && (!text || text === '' || text.length === 0)
      );
    },
    handleHolder(text, index, currentIndex) {
      return this.showHolder(text, index, currentIndex) ? '|' : text;
    },
    /**
     * 重置Mode
     */
    resetMode() {
      this.model = this.model === '1' ? '2' : '1';
    },
    /**
     * 车牌显示模式切换
     */
    onModeChanged() {
      this.$emit('modechanged');
    },
    /**
     * 车牌号单元格选中事件
     * @param {Number} index 选中单元格下标
     */
    onCellSelected(index) {
      this.$emit('cellselected', index, true);
    }
  }
};
</script>

<style lang="scss">
.vehicle-input-widget {
  display: -webkit-box;
  display: -webkit-flex;
  display: flex;
  flex-direction: column;
  flex-wrap: nowrap;
  text-align: center;
  justify-content: center;
  background: none !important;

  .input-area {
    height: 5.5625rem;
    background: #ffffff;
    display: flex;
    align-items: center;
    ul.inputrow {
      -webkit-box-flex: 1;
      -webkit-flex: 1;
      flex: 1;
      display: -webkit-box;
      display: -webkit-flex;
      display: flex;
      flex-direction: row;
      flex-wrap: nowrap;
      list-style: none;
      padding: 0;
      margin: 0 1.875rem;
      height: 2.8125rem;
      border: 0.0625rem solid rgba(113, 154, 255, 1);
      border-radius: 0.25rem;
      overflow: hidden;
      li {
        height: 2.8125rem;
        -webkit-box-flex: 1;
        -webkit-flex: 1;
        flex: 1;
        &.cell {
          float: left;
          // line-height: 2.8rem;
          background-color: rgba(113, 154, 255, 1);
          &.lengthof-7 {
            width: calc((100% - 0.0625rem * 8) / 7);
            &:first-child {
              flex: 1.02279;
              width: calc((100% - 0.0625rem * 8) / 7 + 0.0625rem);
            }
          }
          &.lengthof-8 {
            width: calc((100% - 0.0625rem * 9) / 8);
            &:first-child {
              flex: 1.02614;
              width: calc((100% - 0.0625rem * 9) / 8 + 0.0625rem);
            }
          }
          &:first-child:not(.show-holder) {
            button {
              color: #ffffff;
              background-color: transparent;
            }
          }
          button {
            font-size: 1.5625rem;
            color: rgba(113, 154, 255, 1);
            background-color: white;
          }
          button.line-height-180 {
            line-height: 180%;
          }
        }
        &:not(:first-child)[selected='selected'] {
          .key {
            width: calc(100% - 0.0625rem * 2);
            height: calc(100% - 0.0625rem * 2);
            margin-top: 0.0625rem;
          }
        }
        &[selected='selected'].show-holder {
          button {
            font-size: 1.1875rem;
            color: #b2b2b2;
          }
        }
        &:not(:first-child) {
          padding-left: 0.0625rem;
        }
      }
    }
  }

  .mode-switcher {
    margin-top: 1.625rem;
    .mode--label {
      display: inline-block;
      // color: #719aff;
      font-size: 0.9375rem;
      height: 1.3125rem;
      display: inline-flex;
      align-items: center;
      &:first-child {
        margin-right: 1.875rem;
      }
    }
    .mode--radio {
      display: none;
    }
    .mode--radioInput {
      background-color: rgba(178, 178, 178, 0.5);
      border: 0.0625rem solid rgba(178, 178, 178, 1);
      border-radius: 100%;
      display: inline-block;
      width: 0.75rem;
      height: 0.75rem;
      margin-right: 0.3125rem;
      margin-top: -1px;
      vertical-align: middle;
      line-height: 1;
      color: #b2b2b2;
    }
    .mode--radioText {
      color: #b2b2b2;
    }
    .mode--radio:checked + .mode--radioInput {
      background-color: rgba(113, 154, 255, 0.5);
      border: 0.0625rem solid rgba(113, 154, 255, 1);
    }
    .mode--radio:checked + .mode--radioInput + .mode--radioText {
      color: #719aff;
    }
  }
}
</style>