<template>
    <div class="form-group form-group_slider row">
        <div class="col-xs-12 col-sm-2">
            <label class="control-label">{{ labelName }}</label>
        </div>
        <div class="col-xs-12 col-sm-10">
            <div :id="idName" ref="slider"></div>
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
        count: 0
      };
    },
    props: {
      start: {
        type: Number,
        default: 0
      },
      labelName: {
        type: String,
        required: true
      },
      step: {
        type: Number,
        default: 1000
      },
      min: {
        type: Number,
        required: true
      },
      max: {
        type: Number,
        required: true
      },
      suffix: {
        type: String,
        required: true
      },
      idName: {
        type: String,
        required: true
      },
      stepStore: {
        type: String,
        default: ''
      },
    },
    watch: {
      count(value) {
        this.$store.commit(this.stepStore + _.camelCase(this.idName), value);
      }
    },
    mounted() {
      let element = this.$refs.slider,
        self = this;

      noUiSlider.create(element, {
        start: self.start,
        connect: 'lower',
        tooltips: true,
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
    },
    created() {
      this.count = this.start;
    },
  }
</script>

<style></style>