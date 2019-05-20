<template>
  <div>
    <div class="input-group">
      <input
        @copy="preventCopy"
        :type="type"
        class="form-control"
        :placeholder="placeholder"
        @input="validate($event.target.value)"
        :value="value"
      >
      <span class="input-group-btn">
        <button class="btn btn-default" type="button" @click="seePassword=!seePassword">
          <span
            class="glyphicon"
            :class="{'glyphicon-eye-open': !seePassword, 'glyphicon glyphicon-eye-close': seePassword}"
          ></span>
        </button>
      </span>
    </div>

    <div class="row ajustPadding">
      <div class="col-md-6">
        <div class="progress">
          <div class="progress-bar" style="width: 100%" :class="barColorClass">{{passwordForce}}</div>
        </div>
      </div>
      <div class="col-md-6">
        <button
          id="generateStrongPassword"
          class="btn btn-default pull-right"
          @click="generateStrongPassword"
        >gerar senha forte</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["value", "minLenght", "maxLenght", "placeholder"],
  data() {
    return {
      force: 0,
      seePassword: false,
      inputType: "password"
    };
  },
  methods: {
    preventCopy(e) {
      e.preventDefault();
      return false;
    },
    generateStrongPassword() {
      // Hide passwords while generating
      this.seePassword = false;
      // Generate random string with length og 8 caracters
      var str = Math.random()
        .toString(36)
        .substring(2, 8);

      // Upper cases random caracter
      var randomIndexCapitalLetter = this.getRandomInteger(0, str.length - 1);
      str = this.replaceAt(
        str,
        randomIndexCapitalLetter,
        str[randomIndexCapitalLetter].toUpperCase()
      );

      // Get a random special caracter
      var specialCaracters = ["!", "@", "#", "$", "(", ")"];
      var randomIndexSpecial = this.getRandomInteger(
        0,
        specialCaracters.length - 1
      );

      // Insert special caracter at the end of the string
      str += specialCaracters[randomIndexSpecial];
      this.validate(str);

      if (this.force <= 75) this.generateStrongPassword();
      else this.seePassword = true;
    },
    validate(data) {
      this.force = this.validationValidateLength(data) ? 5 : 0;
      this.force += this.validationContainsNumber(data) ? 15 : 0;
      this.force += this.validationContainsSpecialCaracteres(data) ? 30 : 0;
      this.force += this.validationContainsCapitalLetters(data) ? 25 : 0;
      this.force += this.validationContainsSmallLetters(data) ? 25 : 0;
      
      // Emit event to update v-model
      this.$emit("input", data);
    },
    validationValidateLength(pass) {
      return (
        pass.length >= (this.ninLenght || 4) &&
        pass.length <= (this.ninLenght || 20)
      );
    },
    validationContainsNumber(pass) {
      return pass.match(/\d+/g) && pass.match(/\d+/g).length > 0;
    },
    validationContainsSpecialCaracteres(pass) {
      return pass.match(/\W+/g) && pass.match(/\W+/g).length > 0;
    },
    validationContainsCapitalLetters(pass) {
      return pass.match(/\w*[A-Z]+/g) && pass.match(/\w*[A-Z]+/g).length > 0;
    },
    validationContainsSmallLetters(pass) {
      return pass.match(/\w*[a-z]+/g) && pass.match(/\w*[a-z]+/g).length > 0;
    },
    getRandomInteger(min, max) {
      return Math.floor(Math.random() * (+max - +min)) + +min;
    },
    replaceAt(src, index, replacement) {
      return (
        src.substr(0, index) +
        replacement +
        src.substr(index + replacement.length)
      );
    }
  },
  computed: {
    barColorClass() {
      if (this.force == 0) return "progress-bar-default";

      var cssClass = "progress-bar-danger";
      if (this.force > 30) cssClass = "progress-bar-warning";
      if (this.force > 75) cssClass = "progress-bar-success";

      return cssClass;
    },
    type() {
      return this.seePassword ? "text" : "password";
    },
    passwordForce() {
      if (this.force == 0) return "informe uma senha forte";

      var text = "Fraco";
      if (this.force > 30) text = "Médio";
      if (this.force > 75) text = "Forte";

      return text;
    }
  },
  mounted() {
    this.validate(this.value);
  },
};
</script>

<style scoped>
.ajustPadding {
  padding-left: 0;
  padding-top: 15px;
}

#generateStrongPassword {
  padding: 1px 20px;
  font-size: 13px;
}
</style>