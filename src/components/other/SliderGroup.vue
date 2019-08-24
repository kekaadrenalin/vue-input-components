<template>
    <div class="form-group form-group_slider row">
        <div class="col-xs-12 col-sm-3 col-lg-2">
            <label class="control-label">{{ labelName }}</label>
            <span :class="classSliderTooltip">{{ currentCount }}</span>
        </div>
        <div class="col-xs-12 col-sm-9 col-lg-10">
            <div :id="idName" :class="classSliderBlock" ref="slider"></div>
            <input type="hidden" class="hide" ref="inputElement">
        </div>
    </div>
</template>

<script>
  import _ from 'lodash';
  import wNumb from 'wnumb';
  import noUiSlider from 'nouislider';

  export default {
    name: 'slider-group',
    data() {
      return {
        count: 0,
        currentCount: 0,
      };
    },
    props: {
      /**
       * стартовое значение
       */
      start: {
        type: Number,
        default: 0
      },

      /**
       * label заголовок
       */
      labelName: {
        type: String,
        required: true
      },

      /**
       * шаг ползунка
       */
      step: {
        type: Number,
        default: 1000
      },

      /**
       * min значение
       */
      min: {
        type: Number,
        required: true
      },

      /**
       * max значение
       */
      max: {
        type: Number,
        required: true
      },

      /**
       * суффикс значения
       */
      suffix: {
        type: String,
        required: true
      },

      /**
       * атрибут id
       */
      idName: {
        type: String,
        required: true
      },

      /**
       * класс tooltip для slider
       */
      classSliderTooltip: {
        type: String,
        default: 'slider-tooltip'
      },

      /**
       * класс блока с slider
       */
      classSliderBlock: {
        type: String,
        default: 'slider-block'
      },

      /**
       * префикс для vuex commit-а
       */
      stepStore: {
        type: String,
        default: ''
      },

      /**
       * режим отладки
       */
      isDebug: {
        type: Boolean,
        default: false
      },
    },
    watch: {
      count(value) {
        if (!this.isDebug && this.stepStore) {
          this.$store.commit(this.stepStore + _.camelCase(this.idName), value);
        }
      }
    },
    mounted() {
      let element = this.$refs.slider,
        self = this;

      noUiSlider.create(element, {
        start: self.start,
        connect: 'lower',
        tooltips: false,
        step: self.step,
        format: wNumb({
          decimals: 0,
          thousand: ' ',
          suffix: self.suffix
        }),
        range: {
          min: self.min,
          max: self.max
        }
      });

      element.noUiSlider.on('end', function (values, handle, unencoded) {
        self.count = Math.round(unencoded[handle]);
      });

      element.noUiSlider.on('update', function (values, handle) {
        self.currentCount = values[handle];
      });
    },
    created() {
      this.count = this.start;
    },
  }
</script>