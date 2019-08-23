<template>
    <div class="row">
        <div class="col-xs-12 col-sm-6">
            <kladr-region label-name="Регион" :id-name="idPrefixSafe + 'region'"
                          :step-store="stepStore"
                          :ref="idPrefixSafe + 'region'"
                          is-required
                          :is-disabled="disabled"
                          :start="startRegion"
                          @change-region="changeRegion"></kladr-region>
        </div>

        <div class="col-xs-12 col-sm-6">
            <kladr-city label-name="Город" :id-name="idPrefixSafe + 'city'"
                        :step-store="stepStore"
                        :ref="idPrefixSafe + 'city'"
                        is-required
                        :start="startCity"
                        :region-id="regionId"
                        :is-disabled="cityDisabled || disabled"></kladr-city>
        </div>

        <div class="col-xs-12 col-sm-6">
            <!--kladr-input label-name="Улица" :id-name="idPrefixSafe + 'street'"
                         content-type="street"
                         :parents="parents"
                         :step-store="stepStore"
                         :ref="idPrefixSafe + 'street'"
                         :start="startStreet"
                         :is-disabled="disabled"
                         is-required></kladr-input-->

            <input-text-group label-name="Улица" :id-name="idPrefixSafe + 'street'"
                              :step-store="stepStore"
                              :ref="idPrefixSafe + 'street'"
                              :start="startStreet"
                              :is-disabled="disabled"
                              is-required></input-text-group>
        </div>

        <div class="col-xs-4 col-sm-2 xs-non-p-r">
            <input-text-group label-name="Дом" :id-name="idPrefixSafe + 'house'"
                              is-not-use-space
                              :step-store="stepStore"
                              :ref="idPrefixSafe + 'house'"
                              :start="startHouse"
                              :is-disabled="disabled"
                              :min-length="0"
                              is-required></input-text-group>
        </div>

        <div class="col-xs-4 col-sm-2 xs-non-p-r xs-non-p-l">
            <input-text-group label-name="Строение" :id-name="idPrefixSafe + 'structure'"
                              :is-disabled="disabled"
                              is-not-use-space
                              :step-store="stepStore"
                              :ref="idPrefixSafe + 'structure'"
                              :min-length="0"
                              :start="startStructure"></input-text-group>
        </div>

        <div class="col-xs-4 col-sm-2 xs-non-p-l">
            <input-text-group label-name="Квартира" :id-name="idPrefixSafe + 'flat'"
                              :is-disabled="disabled"
                              :min-length="0"
                              is-not-use-space
                              :step-store="stepStore"
                              :ref="idPrefixSafe + 'flat'"
                              :start="startFlat"></input-text-group>
        </div>
    </div>
</template>

<script>
  import _ from "lodash";
  import InputTextGroup from "../input/InputTextGroup";
  import KladrRegion from "./KladrRegion";
  import KladrCity from "./KladrCity";
  // import KladrInput from "./KladrInput";

  export default {
    name: 'address-kladr-group',
    data() {
      return {
        idPrefixSafe: '',
        cityDisabled: true,
        regions: [],
        cities: []
      };
    },
    props: {
      idPrefix: {
        type: String,
        default: 'address'
      },
      stepStore: {
        type: String,
        default: 'address'
      },
      startRegion: {
        type: [Number, Boolean],
        default: false
      },
      startCity: {
        type: [Number, Boolean],
        default: false
      },
      startStreet: {
        type: [String],
        default: ''
      },
      startHouse: {
        type: [String],
        default: ''
      },
      startStructure: {
        type: [String],
        default: ''
      },
      startFlat: {
        type: [String],
        default: ''
      },
      disabled: {
        type: Boolean,
        default: false
      }
    },
    computed: {
      regionStore() {
        return this.$store.getters[this.stepStore + _.camelCase(this.idPrefixSafe + 'region')]
      },
      /*
      cityStore() {
        return this.$store.getters[this.stepStore + _.camelCase(this.idPrefixSafe + 'city')]
      },
      */
      regionId() {
        return this.regionStore.value || 0;
      },
      /*
      cityId() {
        return this.cityStore.value || 0;
      },
      parents() {
        return {
          region: this.regionId,
          city: this.cityId,
        }
      },
      */
    },
    methods: {
      changeRegion: function (id) {
        this.cityDisabled = !id;
      },
    },
    created() {
      this.idPrefixSafe = this.idPrefix + '-';
    },
    components: {
      // KladrInput,
      KladrRegion,
      KladrCity,
      InputTextGroup,
    }
  }
</script>

<style></style>