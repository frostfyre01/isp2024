<template>
  <v-container class="fill-height">
    <v-responsive class="align-center text-center fill-height">

      <div class="text-body-2 font-weight-light mb-n1">Welcome to</div>
      <h1 class="text-h2 font-weight-bold">Isabelle's ISP</h1>
      <v-row class="d-flex align-center justify-center">
        <v-col cols="12" md="4">
          <v-alert v-show="showMessage" closable :text="message" :type="alertType"></v-alert>
          <v-img v-show="showMessage" :src="imageSrc"></v-img>
        </v-col>
      </v-row>

      <v-row class="d-flex align-center justify-center">
        <v-col cols="12" md="4">
          <v-select
            clearable
            label="Select"
            v-model="predict_model"
            :items="[
              {title: 'Lung Cancer', value: 'GSE137140'},
              // {title: 'Bladder Cancer', value: 'GSE113486'},
              {title: 'Esophageal Cancer', value: 'GSE122497'},
              {title: 'Gastric Cancer', value: 'GSE164174'}
            ]"
          ></v-select>
        </v-col>
      </v-row>
      <v-row class="d-flex align-center justify-center">
      <v-col cols="12" md="4">
          <v-text-field clearable 
            v-model="MIMAT0019776"
            :counter="10"
            label="MIMAT0019776"
            hide-details
            required
          ></v-text-field>
        </v-col>
      </v-row>
      <v-row class="d-flex align-center justify-center">
        <v-col cols="12" md="4">
          <v-text-field clearable 
            v-model="MIMAT0022259"
            :counter="10"
            label="MIMAT0022259"
            hide-details
            required
          ></v-text-field>
        </v-col>
    </v-row>
      <v-row class="d-flex align-center justify-center">
        <v-btn @click="predict">
        Predict
        </v-btn>
      </v-row>
    </v-responsive>
  </v-container>
</template>


<script setup>
import axios from 'axios';
import { ref } from 'vue'

const predict_model = ref(null)
const MIMAT0019776 = ref('')
const MIMAT0022259 = ref('')
const showMessage = ref(false)
const imageSrc = ref('')
const alertType = ref('')
const message = ref('')

async function predict() {
  showMessage.value = false;
  let formData = new FormData();
      formData.append('MIMAT0019776', MIMAT0019776.value);
      formData.append('MIMAT0022259', MIMAT0022259.value);
      formData.append('model', predict_model.value)

      try {
        axios.post(`https://isalee319818.pythonanywhere.com/predict`, formData)
        .then(
          response => {
            let data = JSON.parse(response.data.replaceAll("'","\""));
            showMessage.value = true;
            imageSrc.value = data.model.image;
            if (data.prediction == 1) {
              alertType.value = 'warning';
              message.value = 'high chance';
            } else {
              alertType.value = 'success';
              message.value = 'low chance';
            }
          }

        )

      } catch (e) {
        this.error = 'Something error found !';
      }
}
</script>
