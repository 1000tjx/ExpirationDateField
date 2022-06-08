<template>
  <div class="d-flex bg-white">
    <input
      :value="value"
      @input="handleValue"
      class="expire-input"
      type="text"
      pattern="[0-9/]{0,5}"
      maxlength="5"
      placeholder="MM/YY"
      inputmode="numeric"
    />
  </div>
</template>

<script>
export default {
  name: "ExpirationDateField",
  props: ["value", "minYear"],
  emit: ["input", "extractData"],
  data() {
    this.$emit("extractData", { month: 1, year: this.minYear || 22 });
    return { oldVal: "" };
  },
  methods: {
    padValue(val) {
      return (val || "00").toString().padStart(2, "0");
    },
    handleValue(e) {
      let val = e.target.value;
      let newVal = val;

      // handle 3rd slash/num
      if (newVal.length == 3) {
        let thirdChar = newVal.substr(2, 3);
        if (thirdChar != "/") {
          newVal = newVal.substr(0, 2) + "/" + thirdChar;
        }
      }

      // month part
      let monthPart = newVal.substr(0, 2);
      monthPart = monthPart.replace(/\D/g, "");
      let monthAsNumber = parseInt(monthPart);
      if (!isNaN(monthAsNumber) && monthPart.length > 1) {
        if (monthAsNumber <= 0) {
          monthPart = "01";
        } else if (monthAsNumber > 12) {
          monthPart = "12";
        }
      }
      if (isNaN(monthAsNumber)) {
        if (monthPart.length == 1) {
          monthPart = "";
        } else if (monthPart.length == 2) {
          monthPart = "01";
        }
      }

      // year part
      let yearPart = newVal.substr(3, 4);
      yearPart = yearPart.replace(/\D/g, "");
      let yearAsNumber = parseInt(yearPart);
      if (!isNaN(yearAsNumber) && yearPart.length > 1) {
        if (yearAsNumber < this.getMinYear) {
          yearPart = this.getMinYear.toString();
        } else if (yearAsNumber > 99) {
          yearPart = "99";
        }
      }
      if (isNaN(yearAsNumber)) {
        if (yearPart.length >= 1) {
          yearPart = "";
        } else if (yearPart.length == 2) {
          yearPart = this.getMinYear.toString();
        }
      }

      if (yearPart.length >= 1) {
        newVal = monthPart + "/" + yearPart;
      } else {
        if (monthPart.length == 2 && newVal.length >= this.oldVal.length) {
          newVal = monthPart + "/";
        } else {
          newVal = monthPart;
        }
      }

      queueMicrotask(() => {
        this.$emit("input", newVal);
        this.$emit("extractData", {
          month: parseInt(monthPart),
          year: parseInt(yearPart),
        });
        this.oldVal = newVal;
        this.$forceUpdate();
      });
    },
  },
  computed: {
    getMinYear() {
      return this.minYear || 22;
    },
  },
};
</script>

<style scoped>
input,
input:focus {
  outline: none !important;
  border: 0px !important;
}
.expire-input {
  font-size: 1em;
  max-width: 80%;
  padding: 3px;
}
.expire-input::-webkit-outer-spin-button,
.expire-input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
</style>
