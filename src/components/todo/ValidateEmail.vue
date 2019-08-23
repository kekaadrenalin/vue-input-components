<template>
    <div class="validate-email-block text-center">
        <hr>
        <label class="form-label">{{ labelText }}</label>

        <div v-if="step === 1">
            <button class="btn btn-next"
                    :disabled="isDisabledSenderEmailBtn"
                    @click.prevent="sendEmail">Отправить проверочный код
            </button>
        </div>

        <div v-if="step === 2" class="row">
            <div class="col-md-3"></div>
            <div class="col-xs-12 col-md-4">
                <div class="form-group">
                    <input class="form-control" v-model.trim="code">
                </div>
            </div>

            <div class="col-xs-12 col-md-2">
                <button class="btn btn-next"
                        :disabled="isDisabledSenderCodeBtn"
                        @click.prevent="sendCode">Подтвердить email
                </button>
            </div>
        </div>
        <hr>
    </div>
</template>

<script>
  import axios from 'axios';

  export default {
    name: "ValidateEmail",
    data() {
      return {
        step: 1,
        code: '',
        uid: '',
        labelText: '',
        isDisabledSenderEmailBtn: false,
        isDisabledSenderCodeBtn: false,
      }
    },
    props: {
      /**
       * Текст перед отправкой email на валидацию
       */
      beforeValidateText: {
        type: String,
        default: 'Для продолжения необходимо подтвердить указанный email'
      },

      /**
       * Текст перед отправкой проверочного кода
       */
      afterValidateText: {
        type: String,
        default: 'Для продолжения необходимо ввести код подтверждения'
      },

      /**
       * Url для валидации
       */
      url: {
        type: String,
        required: true
      },

      /**
       * Имя геттерса для проверки
       */
      keyStore: {
        type: String,
        default: ''
      },
    },
    computed: {
      email() {
        return this.$store.getters[this.keyStore]
      }
    },
    watch: {
      email: function (oldValue, newValue) {
        if (oldValue !== newValue) {
          this.labelText = this.beforeValidateText;
          this.step = 1;
          this.uid = null;
        }
      },
    },
    methods: {
      sendEmail() {
        this.isDisabledSenderEmailBtn = true;

        axios.post(this.url, {
          email: this.email
        })
          .then((e) => {
            this.isDisabledSenderEmailBtn = false;

            if (e.status === 200 && e.data.uid !== undefined) {
              this.uid = e.data.uid;
              this.labelText = this.afterValidateText;
              this.step = 2;
            }
          })
      },
      sendCode() {
        this.isDisabledSenderCodeBtn = true;

        axios.post(this.url, {
          email: this.email,
          code: this.code,
          uid: this.uid,
        })
          .then((e) => {
            this.isDisabledSenderCodeBtn = false;

            if (e.status === 200 && e.data.status === 'ok') {
              this.$emit('email-is-valid');
            }
          })
      },
    },
    created() {
      this.labelText = this.beforeValidateText;
    }
  }
</script>

<style></style>