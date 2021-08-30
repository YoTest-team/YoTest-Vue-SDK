<template>
  <div ref="captcha" class="yotest"></div>
</template>

<script>
import initYoTest from "yotest-web-sdk";
export default {
  data() {
    return {
      $captcha: null,
    };
  },
  props: {
    accessId: {
      type: String,
      required: true,
    },
    product: {
      type: String,
      default: "float",
    },
    area: String,
    bgColor: String,
    enforced: {
      type: Boolean,
      default: false,
    },
  },
  created() {
    initYoTest(
      {
        accessId: this.accessId,
        product: this.product,
        area: this.area,
        bgColor: this.bgColor,
        enforced: this.enforced,
      },
      (captcha) => {
        this.$nextTick(() => this.onLoadedHandler(captcha));
      }
    );
  },
  methods: {
    getValidate() {
      if (!this.$captcha) {
        return;
      }
      return this.$captcha.getValidate();
    },
    reset() {
      if (!this.$captcha) {
        return;
      }
      return this.$captcha.reset();
    },
    verify() {
      if (!this.$captcha) {
        return;
      }
      return this.$captcha.verify();
    },
    destroy() {
      if (!this.$captcha) {
        return;
      }
      this.$captcha.destroy();
      this.$captcha = null;
    },
    onLoadedHandler(captcha) {
      if (!captcha) {
        this.$emit("error", { code: -1, message: "loaded error" });
        return;
      }

      this.$captcha = captcha;
      captcha.onReady(() => this.$emit("ready"));
      captcha.onSuccess((data) => this.$emit("success", data));
      captcha.onError((data) => this.$emit("error", data));
      captcha.onClose(() => this.$emit("close"));
      if (this.product !== "bind") {
        const classId = `j_yotest_${Date.now()}_${(Math.random() * 1024) | 0}`;
        const { captcha: $captcha } = this.$refs;
        $captcha.className += ` ${classId}`;
        this.$captcha.appendTo(`.${classId}`);
      }
    },
  },
};
</script>

<style scoped>
.yotest {
  width: 300px;
  height: 40px;
}
</style>