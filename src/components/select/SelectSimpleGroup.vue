<template>
    <div class="form-group" :class="[classObject, 'field-' + idName]">
        <label class="control-label" :for="idName">{{ labelName }} <span v-if="hasError">ОШИБКА!!!</span></label>
        <select class="form-control" :id="idName" ref="inputElement" v-model="selected" :required="isRequired"
                :disabled="isDisabled" @focus="onBlur = true">
            <option value="0" disabled>Выбрать...</option>
            <option v-for="option in data" :value="option.id">{{ option.text }}</option>
        </select>
    </div>
</template>

<script>
  import _ from 'lodash';

  export default {
    name: 'select-simple-group',
    data: function () {
      return {
        selected: '',
        onBlur: false
      };
    },
    props: {
      /**
       * список значений
       */
      data: {
        type: Array,
        required: true
      },

      /**
       * начальное значение
       */
      start: {
        type: [String, Number],
        default: null
      },

      /**
       * обязательное ли поле
       */
      isRequired: {
        type: Boolean,
        default: false
      },

      /**
       * атрибут disabled
       */
      isDisabled: {
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
       * префикс для vuex commit-а
       */
      stepStore: {
        type: String,
        default: ''
      },
    },
    watch: {
      selected: function () {
        if (this.selected !== 0 && !this.hasError) {
          this.$store.commit(this.stepStore + _.camelCase(this.idName), this.selected);
        }
      },
    },
    computed: {
      hasError: function () {
        if (this.selected === 0 && this.isRequired && this.onBlur) return true;
      },
      hasSuccess: function () {
        if (this.selected !== 0 && this.onBlur && !this.hasError) return true;
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
      this.selected = this.start;

      /*
      if (null === this.selected && this.dataSafe.length) {
        this.selected = _.head(this.dataSafe).id;
      }
      */

      this.selected = 0;
    }
  }
</script>

<style></style>