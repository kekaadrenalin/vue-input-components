<template>
    <div class="form-group" :class="[classObject, 'field-' + idName]">
        <select class="form-control" :id="idName" ref="inputElement" v-model="selected" :required="isRequired"
                @focus="onBlur = true">
            <option value="0" disabled>Выбрать...</option>
            <option v-for="option in month" :value="option.id">{{ option.text }}</option>
        </select>
    </div>
</template>

<script>
  import _ from 'lodash';

  export default {
    name: 'select-month-group',
    data: function () {
      return {
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
        selected: '',
        onBlur: false
      };
    },
    props: {
      isRequired: {
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
    },
    watch: {
      selected: function (value) {
        if (this.selected > 0 && !this.hasError) {
          this.$store.commit(this.stepStore + _.camelCase(this.idName), value);
        }
      }
    },
    computed: {
      hasError: function () {
        if (this.selected < 1 && this.isRequired && this.onBlur) return true;
      },
      hasSuccess: function () {
        if (this.selected > 0 && this.onBlur && !this.hasError) return true;
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
      this.selected = 0;
    }
  }
</script>

<style></style>