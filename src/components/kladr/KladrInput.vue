<template>
    <div class="form-group" :class="[classObject, 'field-' + idName]">
        <label class="control-label" :for="idName">{{ labelName }}</label>
        <input class="form-control" v-model.trim="input" :id="idName" :required="isRequired" ref="inputElement"
               @blur.prevent="handleOnBlur" @focus.prevent="handleOnFocus" @keydown.esc="handleKeyClickEsc"
               @keyup.up.prevent="handleKeyClickUp" @keyup.down.prevent="handleKeyClickDown"
               @keyup.enter.prevent="handleKeyClickEnter" @input="handleOnKeyUp" :placeholder="placeholder">

        <div class="kladr-select" v-show="result.length && resultActive">
            <div class="kladr-select__item" :class="activeClass(i)" v-for="(item, i) in result" :key="item.id + i"
                 v-html="getLabel(item)" @mousemove="mouseMove(i)" @click="handleKeyClickEnter"></div>
        </div>

        {{parents}}
    </div>
</template>

<script>
  import _ from 'lodash'
  import axios from 'axios-jsonp-pro'

  export default {
    name: 'kladr-input',
    data: function () {
      return {
        focusList: 0,
        isResultKeyUp: false,
        resultActive: false,
        onBlur: false,
        query: false,
        input: '',
        result: [],
        typeConfig: {
          region: false,
          city: 'region',
          street: 'city',
          building: 'street'
        }
      };
    },
    props: {
      isRequired: {
        type: Boolean,
        default: false
      },
      placeholder: {
        type: String,
        default: ''
      },
      parents: {
        type: [Object, Boolean],
        default: false
      },
      contentType: {
        type: String,
        required: true
      },
      labelName: {
        type: String,
        required: true
      },
      idName: {
        type: String,
        required: true
      },
      stepStore: {
        type: String,
        required: true
      },
      start: {
        type: String,
        default: ''
      },
    },
    computed: {
      hasError: function () {
        if (this.input === '' && this.isRequired && this.onBlur) return true;
      },
      hasSuccess: function () {
        if (this.input !== '' && this.onBlur && !this.hasError) return true;
      },
      classObject: function () {
        return {
          'has-error': this.hasError,
          'has-success': this.hasSuccess,
          'required': this.isRequired
        };
      }
    },
    watch: {
      query: function (newInput, oldInput) {
        if (!this.isResultKeyUp && oldInput !== newInput && newInput !== '')
          this.getResults();

        if (newInput.length === 0)
          this.handleKeyClickEsc();
      },
      start() {
        this.query = this.input = this.start ? this.start : '';
      },
    },
    methods: {
      getResults: _.debounce(
        function () {
          let self = this,
            params = {
              token: '566fce070a69de906d8b45b4',
              contentType: this.contentType,
              query: this.query,
              limit: 10
            };

          this.$store.commit(this.stepStore + _.camelCase(this.idName), this.input);

          if (this.parents) {
            _.forEach(this.parents, function (value, key) {
              if (value && self.typeConfig[self.contentType] && self.typeConfig[self.contentType] === key) params[key + 'Id'] = value;
            });
          }

          /** @namespace params.cityId */
          if (this.contentType === 'street' && params.cityId === undefined) {
            return false;
          }

          //console.log(params)

          axios.jsonp('https://kladr-api.ru/api.php', {
            params: params
          })
            .then(function (response) {
              self.setResult(response.result);

              //console.log(response.result)
            })
            .catch(function (error) {
              console.log(error);
            });
        }, 450
      ),
      setResult: function (result) {
        if (_.isArray(result)) {
          this.result = (result.length > 0) ? result : [];
        }
      },
      getLabel: function (obj) {
        let query = this.query,
          label = '',
          name,
          objName,
          queryName,
          start;

        if (obj.typeShort) {
          label += obj.typeShort + '. ';
        }

        name = obj.name;
        objName = _.lowerCase(name);
        queryName = _.lowerCase(query);
        start = objName.indexOf(queryName);
        start = ~start ? start : 0;

        if (queryName.length < objName.length) {
          label += name.substr(0, start);
          label += '<strong>';
          label += name.substr(start, queryName.length);
          label += '</strong>';
          label += name.substr(start + queryName.length);
        } else {
          label += '<strong>' + name + '</strong>';
        }

        return label;
      },
      clearInput: function () {
        this.query = this.input;
        this.resultActive = false;
        this.isResultKeyUp = false;
        this.onBlur = true;
        this.focusList = 0;
        this.result = [];
      },
      handleOnKeyUp: function () {
        this.query = this.input;
      },
      handleOnBlur: _.debounce(function () {
        if (this.isResultKeyUp) {
          this.handleKeyClickEnter();
        } else {
          this.clearInput();
        }
      }, 150),
      handleOnFocus: function () {
        this.resultActive = true;
        this.onBlur = false;
      },
      filterFocusList: function () {
        this.isResultKeyUp = true;

        const listLength = this.result.length - 1;
        const outOfRangeBottom = this.focusList > listLength;
        const outOfRangeTop = this.focusList < 0;
        const topItemIndex = 0;
        const bottomItemIndex = listLength;

        let nextFocusList = this.focusList;
        if (outOfRangeBottom) nextFocusList = topItemIndex;
        if (outOfRangeTop) nextFocusList = bottomItemIndex;

        this.focusList = nextFocusList;
        this.input = !this.result.length ? '' : this.result[this.focusList].name;
      },
      handleKeyClickEsc: function () {
        this.input = '';
        this.result = [];

        this.$emit('change', {
          id: false,
          type: this.contentType
        });
      },
      handleKeyClickDown: function () {
        this.focusList++;
        this.filterFocusList();
      },
      handleKeyClickUp: function () {
        this.focusList--;
        this.filterFocusList();
      },
      handleKeyClickEnter: function () {
        const _item = !this.result.length ? false : this.result[this.focusList];
        this.selectItem(_item);
      },
      selectItem: function (item) {
        this.input = item.name;

        if (this.$refs.inputElement.focused) {
          this.$refs.inputElement.blur();
        } else {
          this.clearInput();
        }

        this.$emit('change', {
          id: item.id,
          type: this.contentType
        });
      },
      mouseMove: function (i) {
        this.focusList = i;
      },
      activeClass: function (i) {
        return i === this.focusList ? 'active' : '';
      }
    },
  }
</script>

<style></style>