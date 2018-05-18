<template>
    <div class="row">
        <div class="col-xs-12 col-sm-6">
            <kladr-region label-name="Регион" :id-name="idPrefixSafe + 'region'" :step-store="stepStore" ref="region"
                          is-required :is-disabled="disabled" :start="startRegion"
                          @change-region="changeRegion"></kladr-region>
        </div>
        <div class="col-xs-12 col-sm-6">
            <kladr-city label-name="Город" :id-name="idPrefixSafe + 'city'" :step-store="stepStore" ref="city"
                        is-required :start="startCity" :is-disabled="cityDisabled || disabled"></kladr-city>
        </div>
        <div class="col-xs-12 col-sm-6">
            <input-text-group label-name="Улица" :id-name="idPrefixSafe + 'street'"
                              :step-store="stepStore" ref="street" :start="startStreet" :is-disabled="disabled"
                              is-required></input-text-group>
        </div>
        <div class="col-xs-4 col-sm-2 xs-non-p-r">
            <input-text-group label-name="Дом" :id-name="idPrefixSafe + 'house'" is-not-use-space
                              :step-store="stepStore" ref="house" :start="startHouse" :is-disabled="disabled"
                              is-required></input-text-group>
        </div>
        <div class="col-xs-4 col-sm-2 xs-non-p-r xs-non-p-l">
            <input-text-group label-name="Строение" :id-name="idPrefixSafe + 'structure'" :is-disabled="disabled"
                              is-not-use-space :step-store="stepStore" ref="structure"
                              :start="startStructure"></input-text-group>
        </div>
        <div class="col-xs-4 col-sm-2 xs-non-p-l">
            <input-text-group label-name="Квартира" :id-name="idPrefixSafe + 'flat'" :is-disabled="disabled"
                              is-not-use-space :step-store="stepStore" ref="flat" :start="startFlat"></input-text-group>
        </div>
    </div>
</template>

<script>
  import InputTextGroup from './InputTextGroup';
  import KladrRegion from "./KladrRegion";
  import KladrCity from "./KladrCity";

  export default {
    name: 'address-kladr-group',
    data() {
      return {
        idPrefixSafe: '',
        cityDisabled: true,
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
        type: [Boolean, String],
        default: ''
      },
      startHouse: {
        type: [Boolean, String],
        default: ''
      },
      startStructure: {
        type: [Boolean, String],
        default: ''
      },
      startFlat: {
        type: [Boolean, String],
        default: ''
      },
      disabled: {
        type: Boolean,
        default: false
      }
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
      KladrRegion,
      KladrCity,
      InputTextGroup,
    }
  }
</script>

<style></style>