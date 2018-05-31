<template>
    <div class="form-group" :class="[classObject, 'field-' + idName]">
        <label class="control-label" :for="idName">{{ labelName }}</label>

        <input type="text" class="form-control"

               v-model.trim="input"

               :id="idName"
               :placeholder="placeholder"

               :required="isRequired"
               :disabled="isDisabled"

               @blur="eventBlur"
               @focus="onBlur = false"

               ref="inputElement">
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
      /**
       * начальное значение
       */
      start: {
        type: [String, Number, Boolean],
        default: '',
      },

      /**
       * маска
       */
      mask: {
        type: String,
        required: true
      },

      /**
       * обязательное ли поле
       */
      isRequired: {
        type: Boolean,
        default: false
      },

      /**
       * label заголовок
       */
      labelName: {
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
       * атрибут placeholder
       */
      placeholder: {
        type: String,
        default: ''
      },

      /**
       * атрибут disabled
       */
      isDisabled: {
        type: Boolean,
        default: false
      },

      /**
       * префикс для vuex commit-а
       */
      stepStore: {
        type: [String, Boolean],
        default: false
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
      start() {
        this.input = this.start ? this.start : '';
      },
    },
    computed: {
      inputOutput: function () {
        return Inputmask.unmask(this.input, {alias: this.mask});
      },
      hasError: function () {
        if (this.onBlur && !this.validate()) return true;
      },
      hasSuccess: function () {
        if (this.input.length > 0 && this.onBlur && !this.hasError) return true;
      },
      classObject: function () {
        return {
          'has-error': !!this.hasError,
          'has-success': !!this.hasSuccess,
          'required': !!this.isRequired
        };
      }
    },
    methods: {
      eventBlur() {
        this.onBlur = true;

        if (!this.isDebug && this.stepStore && !!this.validate()) {
          this.$store.commit(this.stepStore + _.camelCase(this.idName), _.upperFirst(this.inputOutput));
        }
      },
      validate() {
        if (this.input.length < 1 && this.isRequired && this.onBlur) return false;

        return !(!Inputmask.isValid(this.input, {alias: this.mask}) && this.onBlur);
      },
    },
    created: function () {
      this.input = this.start ? this.start : '';
    },
    mounted: function () {
      const element = this.$refs.inputElement;
      Inputmask({'mask': this.mask}).mask(element);
    }
  }
</script>

<style></style>