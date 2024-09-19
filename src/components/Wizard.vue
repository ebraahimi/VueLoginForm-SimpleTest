<script setup >
   import { ref } from 'vue';
   import axios from 'axios';

   // Data
   const currentStep = ref('username');
   const username = ref('');
   const email = ref('');
   const err = ref(false);
const message = ref('');
const maxUsernameLength = 20;

const isValidEmail = (email) => {
   const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
   return regex.test(email);
};

   
// Methods
const nextStep = () => {
    if (!username.value) {
      err.value = true;
      message.value = 'Username must not be empty.';
   } else if  ( username.value.trim().length > maxUsernameLength || username.value.trim().length < 4) {
      err.value = true;
        message.value = 'Username must be between 4 and 20 characters long.';
   } else if (!/^[a-zA-Z0-9_]+$/.test(username.value)) {
      err.value = true;
      message.value = 'Username must only contain letters and numbers.';
   }else if (currentStep.value === 'username') {
      currentStep.value = 'email';
      err.value = false;
   } else if (!email.value) {
      err.value = true;
      message.value = 'Email must not be empty.';
   } else if (currentStep.value === 'email' && (!email.value || !isValidEmail(email.value))) {
      err.value = true;
      message.value = 'Email must not be empty and must be valid.';
   } else if (currentStep.value === 'email') {
      currentStep.value = 'review';
      err.value = false;
   }
};


   const prevStep = () => {
      if (currentStep.value === 'email') {
         currentStep.value = 'username';
         err.value = false;
      } else if (currentStep.value === 'review') {
         currentStep.value = 'email';
         err.value = false;
      }
};

//Composition API
const submitForm = () => {
   const formData = {
      username: username.value,
      email: email.value
   };
   
   axios.post('/submit-formAPI', formData)
      .then(response => {
         // Handle response from server
         console.log('Form submitted successfully!');
         console.log(formData);
      })
      .catch(error => {
         // Handle error
         console.log('Form submission failed!');
         console.log(formData);
      });
};
</script>
   
<template>
<!-- form -->
   <form action="POST" class="login__form">
      <h1 class="login__title" > Registration form </h1>
      <!-- steps header-->
      <div class="steps">
         <span class="step" :class="{ 'activeStep': currentStep === 'username' }">Step 1</span>
         <span class="step" :class="{ 'activeStep': currentStep === 'email' }">Step 2</span>
         <span class="step" :class="{ 'activeStep': currentStep === 'review' }">Step 3</span>
      </div>

      <!-- inputs -->
        <div class="login__inputs">
            <div class="login__box" v-if="currentStep === 'username'" >
                <input required class="login__input" v-model="username" 
                type="text" id="username" name="username" placeholder="Username">
                <i class="ri-user-2-fill"></i>
            </div>
            <div class="login__box" v-if="currentStep === 'email'">
                <input  required class="login__input" v-model="email" @blur="validateEmail"
                 type="text" id="email" name="email" placeholder="Email">
                <i class="ri-mail-fill"></i>
            </div>
            <p class="error" v-if="err">{{ message }}</p> 
            <!-- review step -->
            <div class="login__box" v-if="currentStep === 'review'">
                <input class="login__input" v-model="username" disabled
                type="text" id="username" name="username" placeholder="{{Username}}">
                <i class="ri-user-2-fill"></i>
            </div>
            <div class="login__box" v-if="currentStep === 'review'">
                <input  class="login__input" v-model="email" disabled
                 type="text" id="email" name="email" placeholder='{{email}}'>
                <i class="ri-mail-fill"></i>
            </div>
        </div>
        <!-- btn -->
      <div class="steps">
         <button class="login__button"  @click="prevStep" :disabled="currentStep === 'username'" id="btn-prev" type="button"> Prev </button>
         <button class="login__button" v-if="currentStep !== 'review'" @click="nextStep" id="btn-next" type="button" :disabled="currentStep === 'review'"> Next </button>
         <button class="login__button" v-if="currentStep === 'review'" @click="submitForm"  id="btn-submit" type="button"> Submit </button>
      </div>
   </form>
</template>