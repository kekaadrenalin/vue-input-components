<template>
    <div :class="[classObject, classWrap, 'field-' + idName]">
        <select :class="classSelect" :id="idName" ref="inputElement" v-model="selected" :required="isRequired"
                @focus="onBlur = true">
            <option value="0" disabled>{{ defaultValue }}</option>
            <option v-for="option in years" :value="option">{{ option }}</option>
        </select>
    </div>
</template>

<script>
  import _ from 'lodash';

  export default {
    name: 'select-year-group',
    data: () => ({
      years: [],
      selected: 0,
      onBlur: false
    }),
    props: {
      /**
       * обязательное ли поле
       */
      isRequired: {
        type: Boolean,
        default: false
      },

      /**
       * атрибут id
       */
      idName: {
        type: String,
        required: true
      },

      /**
       * корректор max даты
       */
      startCorrect: {
        type: Number,
        default: 0
      },

      /**
       * разница между max и min
       */
      diffYears: {
        type: Number,
        default: 90
      },

      /**
       * значение дефолтного option-а
       */
      defaultValue: {
        type: String,
        default: 'Выбрать год...'
      },

      /**
       * class для обертки
       */
      classWrap: {
        type: String,
        default: 'form-group'
      },

      /**
       * class для select
       */
      classSelect: {
        type: String,
        default: 'form-control'
      },

      /**
       * префикс для vuex commit-а
       */
      stepStore: {
        type: String,
        default: ''
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
      selected(value) {
        if (!this.isDebug && this.stepStore && value > 0 && !this.hasError) {
          this.$store.commit(this.stepStore + _.camelCase(this.idName), value);
        }
      }
    },
    computed: {
      hasError() {
        if ((this.selected < 1) && this.isRequired && this.onBlur) return true;
      },
      hasSuccess() {
        if (this.onBlur && !this.hasError) return true;
      },
      classObject() {
        return {
          'has-error': this.hasError,
          'has-success': this.hasSuccess,
          'required': this.isRequired
        };
      }
    },
    created() {
      let currentYear = Number(new Date().getFullYear()) - this.startCorrect,
        endYear = this.diffYears;

      while (endYear > 0) {
        endYear--;
        this.years.push(currentYear--);
      }
    }
  }
</script>

<style></style>