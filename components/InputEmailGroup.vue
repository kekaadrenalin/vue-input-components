<template>
    <div class="form-group" :class="[classObject, 'field-' + idName]">
        <label class="control-label" :for="idName">{{ labelName }}</label>

        <input type="email" class="form-control"

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

  export default {
    name: 'input-email-group',
    data() {
      return {
        input: '',
        onBlur: false,
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
       * максимальная длина строки
       */
      maxLength: {
        type: Number,
        default: 100
      },

      /**
       * минимальная длина строки
       */
      minLength: {
        type: Number,
        default: 6
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
        if (!this.isDebug && this.stepStore)
          this.$store.commit(this.stepStore + _.camelCase(this.idName), _.upperFirst(this.input));

        this.onBlur = true;
      },
      validate() {
        if (this.input.length < 1 && this.isRequired && this.onBlur) return false;

        const input = String(this.input);

        // length
        if (input.length < this.minLength || input.length > this.maxLength) return false;

        // space
        if (/\s/g.test(input)) return false;

        // email
        return ((/^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([а-яёa-z\-0-9]+\.)+[а-яёa-z]{2,}))$/i.test(input)) && this.onBlur);
        // return ((/^(?:[а-яёa-z0-9!#$%&'*+/=?^_`{|}~-]+(?:\.[а-яёa-z0-9!#$%&'*+/=?^_`{|}~-]+)*|"(?:[\x01-\x08\x0b\x0c\x0e-\x1f\x21\x23-\x5b\x5d-\x7f]|\\[\x01-\x09\x0b\x0c\x0e-\x7f])*")@(?:(?:[а-яёa-z0-9](?:[а-яёa-z0-9-]*[а-яёa-z0-9])?\.)+[а-яёa-z0-9](?:[а-яёa-z0-9-]*[а-яёa-z0-9])?|\[(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?|[а-яёa-z0-9-]*[а-яёa-z0-9]:(?:[\x01-\x08\x0b\x0c\x0e-\x1f\x21-\x5a\x53-\x7f]|\\[\x01-\x09\x0b\x0c\x0e-\x7f])+)\])$/i.test(input)) && this.onBlur);
        // return ((/^.+@.+[.].+$/i.test(input)) && this.onBlur);
      },
    },
    created() {
      this.input = this.start ? this.start : '';
    }
  }
</script>

<style></style>