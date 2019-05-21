<template>
  <div class="container">
    <div class="row">
      <div class="col-xs-12 col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3">
        <input type="text" class="form-control">
        <br>
        <strong-password v-model="userData.password" placeholder="Enter password"></strong-password>
        Valor atribuído à propriedade do objeto: {{ userData.password }}        
      </div>
    </div>
  </div>
</template>

<script>
import strongPassword from "./components/StrongPassword.vue";

export default {
  data() {
    return { userData: { password: "12345@aL" } };
  },
  components: { strongPassword: strongPassword }
};
</script>

<style>
</style>
