<template>
  <div>
    <form novalidate class='md-layout' @submit.prevent="validateInput">
      <md-card class="md-layout-item md-small-size-100 card">
        <md-card-header>
          <div class='md-title'>Input iris information</div>
        </md-card-header>
        <md-card-content>
           <div class="md-layout md-gutter">
            <div class="md-layout-item md-size-100 md-small-size-100 md-alignment-center">
              <md-field :class="getValidationClass('sepalLength')">
              <label>Sepal length:</label>
              <md-input type="number" name="sepal-length" id='sepal-length'
                v-model='form.sepalLength'/>
                <span class="md-error" v-if="!$v.form.sepalLength.required">
                  Sepal length is required.
                </span>
                <span class="md-suffix">cm</span>
              </md-field>
            </div>
            <div class="md-layout-item md-size-100 md-small-size-100">
              <md-field :class="getValidationClass('sepalWidth')">
              <label for="sepal-width" >Sepal width:</label>
              <md-input type="number" name="sepal-width" id='sepal-width'
                v-model='form.sepalWidth'/>
                <span class="md-error" v-if="!$v.form.sepalWidth.required">
                  Sepal width is required.
                </span>
                <span class="md-suffix">cm</span>
              </md-field>
            </div>
            <div class="md-layout-item md-size-100 md-small-size-100">
              <md-field :class="getValidationClass('petalLength')">
              <label for="petal-length">Petal length:</label>
              <md-input type="number" name="petal-length" id='petal-length'
                v-model='form.petalLength'/>
                <span class="md-error" v-if="!$v.form.petalLength.required">
                  Petal length is required.
                </span>
                <span class="md-suffix">cm</span>
              </md-field>
            </div>
            <div class="md-layout-item md-size-100 md-small-size-100">
              <md-field :class="getValidationClass('petalWidth')">
              <label for="petal-width">Petal width:</label>
              <md-input type="number" name="petal-width" id='petal-width'
                v-model='form.petalWidth'/>
                <span class="md-error" v-if="!$v.form.petalWidth.required">
                  Petal width is required.
                </span>
                <span class="md-suffix">cm</span>
              </md-field>
            </div>
          </div>
        </md-card-content>
        <md-progress-bar md-mode="indeterminate" v-if="sending" />

        <md-card-actions>
          <md-button type="submit" class="md-primary" :disabled="sending">
            Submit
          </md-button>
        </md-card-actions>
      </md-card>
    </form>
    <div>
      <md-card id='result-card' class="md-layout-item md-small-size-100 card">
      <md-card-header>
          <div class='md-title'>Expected result</div>
      </md-card-header>
      <md-card-content>
          <p>{{ this.result }}</p>
      </md-card-content>
      </md-card>
    </div>
  </div>
</template>
<script scoped>

import Vue from 'vue';
import {
  MdButton,
  MdCard,
  MdField,
  MdProgress }
  from 'vue-material/dist/components';
import 'vue-material/dist/vue-material.min.css';

import { validationMixin } from 'vuelidate';
import {
  required,
} from 'vuelidate/lib/validators';
import axios from 'axios';
import config from '@/config';

Vue.use(MdButton);
Vue.use(MdCard);
Vue.use(MdField);
Vue.use(MdProgress);

const host = config.backendUrl;

export default {
  name: 'FormInput',
  mixins: [validationMixin],
  data: () => ({
    form: {
      sepalLength: null,
      sepalWidth: null,
      petalLength: null,
      petalWidth: null,
    },
    sending: false,
    result: '',
  }),
  validations: {
    form: {
      sepalLength: {
        required,
      },
      sepalWidth: {
        required,
      },
      petalLength: {
        required,
      },
      petalWidth: {
        required,
      },
    },
  },
  methods: {
    getValidationClass(fieldName) {
      const field = this.$v.form[fieldName];
      if (field) {
        return {
          'md-invalid': field.$invalid && field.$dirty,
        };
      }
    },
    clearForm() {
      this.$v.$reset();
      this.form.sepalLength = null;
      this.form.sepalWidth = null;
      this.form.petalLength = null;
      this.form.petalWidth = null;
    },
    validateInput() {
      this.$v.$touch();

      if (!this.$v.$invalid) {
        this.result = '';
        this.submitForm();
      }
    },
    submitForm() {
      const json = {
        sepal_length: this.form.sepalLength,
        sepal_width: this.form.sepalWidth,
        petal_length: this.form.petalLength,
        petal_width: this.form.petalWidth,
      };
      this.sending = true;
      axios['get'](`${host}classification?slWidth=${this.form.sepalWidth}&slLength=${this.form.sepalLength}&plWidth=${this.form.petalWidth}&plLength=${this.form.petalWidth}`, json)
        .then((response) => {
          console.log(response)
          this.result = response
          this.sending = false;
        })
        .catch((e) => {
          this.result = `Error: ${e}`
          this.loading = false;
        });
    },
  },
};
</script>
<style scoped>
.card {
  margin: 20px;
}
</style>

