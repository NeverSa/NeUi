<template>
  <div :style="{'width':width}" class="ne-form">
    <slot></slot>
  </div>
</template>
<script>
export default {
  name: "NeForm",
  data() {
    return { fields: [] };
  },
  props: {
    width: {
      type: String,
      default: "auto"
    },
    model: { type: Object },
    rules: { type: Object }
  },
  provide() {
    return { form: this };
  },
  methods: {
    resetFields() {
      this.fields.forEach(field => field.resetField());
    },
    validate(cb) {
      return new Promise(resolve => {
        let valid = true,
          count = 0;
        this.fields.forEach(field => {
          field.validate("", error => {
            if (error) valid = false;
            if (++count === this.fields.length) {
              resolve(valid);
              if (typeof cb === "function") cb(valid);
            }
          });
        });
      });
    }
  },

  created() {
    this.$on("form-add", field => {
      if (field) this.fields.push(field);
    });
    this.$on("form-remove", field => {
      if (field.prop) this.fields.splice(this.fields.indexOf(field), 1);
    });
  }
};
</script>
<style lang="less" scoped>
.ne-form {
  margin: 0 auto;
}
</style>
