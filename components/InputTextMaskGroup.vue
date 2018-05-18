<template>
    <div class="form-group" :class="[classObject, 'field-' + idName]">
        <label class="control-label" :for="idName">{{ labelName }}</label>
        <input class="form-control" :id="idName" ref="inputElement" v-model.trim="input"
               :required="isRequired" :placeholder="placeholder" @blur="onBlur = true" @focus="onBlur = false">
    </div>
</template>

<script>
  import _ from 'lodash';
  import Inputmask from 'inputmask';

  export default {
    name: 'input-text-mask-group',
    data() {
      return {
        input: '',
        onBlur: false
      };
    },
    props: {
      start: {
        type: String,
        default: ''
      },
      mask: {
        type: String,
        required: true
      },
      placeholder: {
        type: String,
        default: ''
      },
      labelName: {
        type: String,
        required: true
      },
      isRequired: {
        type: Boolean,
        default: false
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
      input: _.debounce(function () {
        if (!this.hasError) {
          this.$store.commit(this.stepStore + _.camelCase(this.idName), this.inputOutput);
        }
      }, 250)
    },
    computed: {
      inputOutput: function () {
        return Inputmask.unmask(this.input, {alias: this.mask});
      },
      hasError: function () {
        if (this.input.length < 1 && this.isRequired && this.onBlur) return true;

        return !Inputmask.isValid(this.input, {alias: this.mask}) && this.onBlur;
      },
      hasSuccess: function () {
        if (this.input.length > 0 && this.onBlur && !this.hasError) return true;
      },
      classObject: function () {
        return {
          'has-error': this.hasError,
          'has-success': this.hasSuccess,
          'required': this.isRequired
        };
      }
    },
    created: function () {
      this.input = this.start;
    },
    mounted: function () {
      const element = this.$refs.inputElement;
      Inputmask({'mask': this.mask}).mask(element);
    }
  }
</script>

<style></style>