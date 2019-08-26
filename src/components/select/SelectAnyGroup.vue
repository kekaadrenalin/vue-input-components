<template>
    <div class="form-group" :class="[classObject, 'field-' + idName]">
        <div class="row">
            <div class="col-xs-12">
                <label class="control-label" :for="idName">{{ labelName }} <span v-if="hasError">ОШИБКА!!!</span></label>
            </div>
            <div class="col-xs-12">
                <select class="form-control" :id="idName" ref="inputElement" v-model="selected" :required="isRequired"
                        @focus="onBlur = true">
                    <option value="0" disabled>Выбрать...</option>
                    <option v-for="option in dataSafe" :value="option.id">{{ option.text }}</option>
                </select>
            </div>
        </div>
    </div>
</template>

<script>
  import _ from 'lodash';

  export default {
    name: 'select-any-group',
    data: function () {
      return {
        dataSafe: [],
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
      selected: function (value) {
        if (this.selected !== 0 && !this.hasError) {
          this.$store.commit(this.stepStore + _.camelCase(this.idName), value);
        }
      }
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
      this.dataSafe = this.data;

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