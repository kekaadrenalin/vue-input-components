<template>
    <div :class="[classObject, classWrap, 'field-' + idName]">
        <select :class="classSelect" :id="idName" ref="inputElement" v-model="selected" :required="isRequired"
                @focus="onBlur = true">
            <option value="0" disabled>{{ defaultValue }}</option>
            <option v-for="option in month" :value="option.id">{{ option.text }}</option>
        </select>
    </div>
</template>

<script>
  import _ from 'lodash';

  export default {
    name: 'select-month-group',
    data: () => ({
      month: [
        {
          id: 1,
          text: 'Января'
        },
        {
          id: 2,
          text: 'Февраля'
        },
        {
          id: 3,
          text: 'Марта'
        },
        {
          id: 4,
          text: 'Апреля'
        },
        {
          id: 5,
          text: 'Мая'
        },
        {
          id: 6,
          text: 'Июня'
        },
        {
          id: 7,
          text: 'Июля'
        },
        {
          id: 8,
          text: 'Августа'
        },
        {
          id: 9,
          text: 'Сентября'
        },
        {
          id: 10,
          text: 'Октября'
        },
        {
          id: 11,
          text: 'Ноября'
        },
        {
          id: 12,
          text: 'Декабря'
        }
      ],
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
       * значение дефолтного option-а
       */
      defaultValue: {
        type: String,
        default: 'Выбрать месяц...'
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
        if (!this.isDebug && this.stepStore && this.selected > 0 && !this.hasError) {
          this.$store.commit(this.stepStore + _.camelCase(this.idName), value);
        }
      }
    },
    computed: {
      hasError() {
        if ((this.selected < 1 || this.selected > 12) && this.isRequired && this.onBlur) return true;
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
    }
  }
</script>

<style></style>