<template>
    <div :class="[classObject, classWrap, 'field-' + idName]">
        <label :class="classLabel" :for="idName">{{ labelName }} <span v-if="hasError">ОШИБКА!!!</span></label>

        <input type="number"

               v-model.number="input"

               :id="idName"
               :class="classInput"
               :placeholder="placeholder"

               :min="minValue"
               :max="maxValue"
               :step="stepValue"

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
    name: 'input-number-group',
    data: () => ({
      input: '',
      onBlur: false,
    }),
    props: {
      /**
       * начальное значение
       */
      start: {
        type: [Number, Boolean],
        default: 0,
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
       * максимальное значение
       */
      maxValue: {
        type: Number,
        default: 10e6
      },

      /**
       * минимальное значение
       */
      minValue: {
        type: Number,
        default: 0
      },

      /**
       * шаг значений
       */
      stepValue: {
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
        this.input = this.start ? this.start : null;
      },
    },
    computed: {
      hasError() {
        if (this.onBlur && !this.validate()) return true;
      },
      hasSuccess() {
        if (String(this.input).length > 0 && this.onBlur && !this.hasError) return true;
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
          this.$store.commit(this.stepStore + _.camelCase(this.idName), Number(this.input));
        }
      },
      validate() {
        if (String(this.input).length < 1 && this.isRequired && this.onBlur) return false;

        // not number
        if (/[^0-9]/g.test(String(this.input))) return false;

        const input = Number(this.input);

        // min + max
        return !(input < this.minValue || input > this.maxValue);
      },
    },
    created() {
      this.input = this.start ? this.start : null;
    }
  }
</script>