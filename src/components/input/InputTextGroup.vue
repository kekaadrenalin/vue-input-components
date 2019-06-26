<template>
    <div :class="[classObject, classWrap, 'field-' + idName]">
        <label :class="classLabel" :for="idName">{{ labelName }}</label>

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

  export default {
    name: 'input-text-group',
    data: () => ({
      input: '',
      onBlur: false,
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
       * обязательное ли поле
       */
      isRequired: {
        type: Boolean,
        default: false
      },

      /**
       * запрет использования пробелов
       */
      isNotUseSpace: {
        type: Boolean,
        default: false
      },

      /**
       * запрет использования любых некириллических символов
       */
      isUseCyrillic: {
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
       * максимальная длина строки
       */
      maxLength: {
        type: Number,
        default: 255
      },

      /**
       * минимальная длина строки
       */
      minLength: {
        type: Number,
        default: 1
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
          this.$store.commit(this.stepStore + _.camelCase(this.idName), _.upperFirst(this.input));
        }
      },
      validate() {
        if (!this.input.length && this.isRequired && this.onBlur) return false;

        const input = String(this.input);

        // space
        if (this.isNotUseSpace && /\s/g.test(input)) return false;

        // cyrillic + symbol
        if (this.isUseCyrillic && /[^а-яё\-]/gi.test(input)) return false;
        if (this.isUseCyrillic && !(/[а-яё]+/gi.test(input))) return false;

        // length
        return !(input.length < this.minLength || input.length > this.maxLength);
      },
    },
    created() {
      this.input = this.start ? this.start : '';
    }
  }
</script>

<style></style>