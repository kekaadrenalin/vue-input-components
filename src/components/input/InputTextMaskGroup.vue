<template>
    <div :class="[classObject, classWrap, 'field-' + idName]">
        <label :class="classLabel" :for="idName">{{ labelName }} <span v-if="hasError">ОШИБКА!!!</span></label>

        <input type="text"

               v-model.trim="input"

               :id="idName"
               :class="classInput"
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
    data: () => ({
      input: '',
      onBlur: false
    }),
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
       * class для обертки
       */
      classWrap: {
        type: String,
        default: 'form-group'
      },

      /**
       * class для label
       */
      classLabel: {
        type: String,
        default: 'form-label'
      },

      /**
       * class для input
       */
      classInput: {
        type: String,
        default: 'form-control'
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
      inputOutput() {
        return Inputmask.unmask(this.input, {alias: this.mask});
      },
      hasError() {
        if (this.onBlur && !this.validate()) return true;
      },
      hasSuccess() {
        if (this.input.length > 0 && this.onBlur && !this.hasError) return true;
      },
      classObject() {
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
    created() {
      this.input = this.start ? this.start : '';
    },
    mounted() {
      const element = this.$refs.inputElement;
      Inputmask({'mask': this.mask}).mask(element);
    },
    beforeDestroy() {
      const element = this.$refs.inputElement;
      if (element.inputmask)
        element.inputmask.remove();
    }
  }
</script>