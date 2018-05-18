<template>
    <div class="form-group" :class="[classObject, 'field-' + idName]">
        <label class="control-label" :for="idName">{{ labelName }}</label>
        <input :type="typeObject" class="form-control" :id="idName" ref="inputElement" v-model.trim="input"
               :required="isRequired" :placeholder="placeholder" :disabled="isDisabled"
               @blur="eventBlur" @focus="onBlur = false">
    </div>
</template>

<script>
  import _ from 'lodash';

  export default {
    name: 'input-text-group',
    data() {
      return {
        input: '',
        onBlur: false,
      };
    },
    props: {
      start: {
        type: [String, Number, Boolean],
        default: '',
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
      isNotUseSpace: {
        type: Boolean,
        default: false
      },
      isUseCyrillic: {
        type: Boolean,
        default: false
      },
      isEmail: {
        type: Boolean,
        default: false
      },
      isNumber: {
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
      isDisabled: {
        type: Boolean,
        default: false
      }
    },
    watch: {
      start() {
        this.input = this.start ? this.start : '';
      },
    },
    computed: {
      hasError() {
        if (this.input.length < 1 && this.isRequired && this.onBlur) return true;

        const input = String(this.input);

        // space
        if (this.isNotUseSpace && /\s/g.test(input)) return true;

        // cyrillic + symbol
        if (this.isUseCyrillic && /[^а-яё]/gi.test(input)) return true;

        // number
        if (this.isNumber && !(/\d/gi.test(input))) return true;

        // email
        if (this.isEmail && !(/^.+@.+\..+$/i.test(input)) && this.onBlur) return true;
      },
      hasSuccess() {
        if (this.input.length > 0 && this.onBlur && !this.hasError) return true;
      },
      typeObject() {
        return this.isEmail ? 'email' : 'text';
      },
      classObject() {
        return {
          'has-error': this.hasError,
          'has-success': this.hasSuccess,
          'required': this.isRequired
        };
      }
    },
    methods: {
      eventBlur() {
        if (this.stepStore)
          this.$store.commit(this.stepStore + _.camelCase(this.idName), _.upperFirst(this.input));

        this.onBlur = true;
      },
      validate() {
        if (this.input.length < 1 && this.isRequired && this.onBlur) return false;

        const input = String(this.input);

        // space
        if (this.isNotUseSpace && /\s/g.test(input)) return false;

        // cyrillic + symbol
        if (this.isUseCyrillic && /[A-Z]/gi.test(input)) return false;

        // email
        return !(this.isEmail && !(/^.+@.+\..+$/i.test(input)) && this.onBlur);
      },
    },
    created() {
      this.input = this.start;
    }
  }
</script>

<style></style>