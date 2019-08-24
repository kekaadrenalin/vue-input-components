<template>
    <div class="form-group" :class="[classObject, 'field-' + idName]">
        <label class="control-label" :for="idName">{{ labelName }}</label>
        <div class="kladr-geo">
            <input class="form-control kladr-geo__input"
                   autocomplete="off"

                   :value='input'
                   @input='evt => input = evt.target.value'

                   :id="idName"
                   :required="isRequired"
                   :disabled="isDisabled || isLoad"
                   :placeholder="placeholder"

                   @blur.prevent="handleOnBlur"
                   @focus.prevent="handleOnFocus"
                   @keydown.esc="handleKeyClickEsc"
                   @keyup.up.prevent="handleKeyClickUp"
                   @keyup.down.prevent="handleKeyClickDown"
                   @keyup.enter.prevent="handleKeyClickEnter"

                   ref="inputElement">
            <span class="kladr-geo__close" @click="handleKeyClickEsc"
                  v-show="input.length && !isLoad && !isDisabled"></span>
            <span class="kladr-geo__circle" v-show="isLoad"></span>
        </div>

        <div class="kladr-select" v-show="resultActive">
            <div class="kladr-select__item" :class="activeClass(i)" v-for="(item, i) in result" :key="item.id + i"
                 v-html="getLabel(item)" @mousemove="mouseMove(i)" @click="handleKeyClickEnter"></div>
            <div class="kladr-select__item" v-show="!result.length">Ничего не найдено</div>
        </div>
    </div>
</template>

<script>
  import _ from 'lodash';

  export default {
    name: 'KladrRegion',
    data: function () {
      return {
        focusList: 0,
        isResultKeyUp: false,
        resultActive: false,
        onBlur: false,
        selected: false,
        isLoad: false,
        input: '',
        result: [],
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
        type: [Boolean, Number],
        default: false
      },
      isDisabled: {
        type: Boolean,
        default: false
      }
    },
    computed: {
      hasError: function () {
        if (!this.selected && this.isRequired && this.onBlur) return true;
      },
      hasSuccess: function () {
        if (this.selected && this.onBlur && !this.hasError) return true;
      },
      classObject: function () {
        return {
          'has-error': this.hasError,
          'has-success': this.hasSuccess,
          'required': this.isRequired
        };
      },
      oldIdRegion: function () {
        return this.$store.getters['all/regionId'];
      },
      resultJson: function () {
        const regions = this.$store.getters['all/regions'];
        if (regions.length && !this.input.length) this.result = regions;

        return regions;
      }
    },
    watch: {
      input: function (newInput, oldInput) {
        if (!this.isResultKeyUp && oldInput !== newInput && newInput !== '')
          this.getResults();

        if (newInput.length === 0) {
          this.result = this.resultJson;
          this.selected = false;
          this.$emit('change-region', false);
          this.$store.commit(this.stepStore + _.camelCase(this.idName), false);
        }
      },
      selected: function (newId) {
        if (newId && newId !== this.oldIdRegion) {
          this.$store.dispatch('all/updateCities', this.selected).then(result => {
            this.$store.commit('all/cities', {result: result, regionId: newId});
          });
        }
      },
      start: function () {
        this.updateStart();
      },
      resultJson: function (regions) {
        this.isLoad = !regions.length;
      }
    },
    methods: {
      getResults: _.debounce(
        function () {
          let query = _.lowerCase(this.input),
            queryTrans = this.autoTranslit(query);

          this.result = _.filter(this.resultJson, function (o) {
            return ~(_.lowerCase(o.full)).indexOf(query) || ~(_.lowerCase(o.full)).indexOf(queryTrans);
          });

          if (!this.result.length) {
            this.selected = false;
            this.$emit('change-region', false);
          }
        }, 250
      ),
      getLabel: function (obj) {
        let query = this.input,
          label = '',
          name,
          objName,
          queryName,
          start;

        if (!/^[а-яё ]*$/i.test(query)) {
          query = this.autoTranslit(query);
        }

        if (obj['prefix_min']) {
          label += obj['prefix_min'] + '. ';
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

        if (obj['postfix_min']) {
          label += ' ' + obj['postfix_min'] + '.';
        }

        return label;
      },
      autoTranslit: function (query) {
        const s = [
          'й', 'ц', 'у', 'к', 'е', 'н', 'г', 'ш', 'щ', 'з', 'х', 'ъ',
          'ф', 'ы', 'в', 'а', 'п', 'р', 'о', 'л', 'д', 'ж', 'э',
          'я', 'ч', 'с', 'м', 'и', 'т', 'ь', 'б', 'ю'
        ];

        const r = [
          'q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'o', 'p', '\\[', '\\]',
          'a', 's', 'd', 'f', 'g', 'h', 'j', 'k', 'l', ';', '\'',
          'z', 'x', 'c', 'v', 'b', 'n', 'm', ',', '\\.'
        ];

        if (!/^[а-яё ]*$/i.test(query)) {
          for (let i = 0; i < r.length; i++) {
            const reg = new RegExp(r[i], 'mig');
            query = query.replace(reg, function (a) {
              return a == a.toLowerCase() ? s[i] : s[i].toUpperCase();
            });
          }
        }

        return query;
      },
      handleOnBlur: _.debounce(function () {
        if (this.isResultKeyUp) {
          this.handleKeyClickEnter();
        } else {
          this.resultActive = false;
        }
        this.onBlur = true;
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
        if (this.result.length && this.resultActive) {
          const _item = this.result[this.focusList];
          this.selectItem(_item);
        }
      },
      selectItem: function (item) {
        this.input = item.name;
        this.selected = Number(item.id);

        this.$store.commit(this.stepStore + _.camelCase(this.idName), this.selected);
        this.$emit('change-region', this.selected);

        if (this.$refs.inputElement.focused) {
          this.$refs.inputElement.blur();
        } else {
          this.clearInput();
        }
      },
      mouseMove: function (i) {
        this.focusList = i;
      },
      activeClass: function (i) {
        return i === this.focusList ? 'active' : '';
      },
      clearInput: function () {
        this.resultActive = false;
        this.isResultKeyUp = false;
        this.onBlur = true;
        this.focusList = 0;
        this.getResults();
      },
      updateStart: function () {
        if (this.start) {
          const _item = _.find(this.result, ['id', String(this.start)]);
          if (_item) this.selectItem(_item);
        } else {
          this.handleKeyClickEsc();
        }
      }
    },
  }
</script>