<template>
    <div class="form-group" :class="[classObject, 'field-' + idName]">
        <select class="form-control" :id="idName" ref="inputElement" v-model="selected" :required="isRequired"
                @focus="onBlur = true">
            <option value="0" disabled>Выбрать...</option>
            <option v-for="option in years" :value="option">{{ option }}</option>
        </select>
    </div>
</template>

<script>
  import _ from 'lodash';

  export default {
    name: 'select-year-group',
    data: function () {
      return {
        years: [],
        selected: '',
        onBlur: false
      };
    },
    props: {
      startCorrect: {
        type: Number,
        default: 0
      },
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
        if (value > 0 && !this.hasError) {
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
      let currentYear = Number(new Date().getFullYear()) - this.startCorrect,
        endYear = 90;
      if (this.selected == null) this.selected = currentYear;

      while (endYear > 0) {
        endYear--;
        this.years.push(currentYear--);
      }

      // this.selected = this.years[0];
      this.selected = 0;
    }
  }
</script>

<style></style>