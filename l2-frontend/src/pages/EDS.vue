<template>
  <div class="root">
    <iframe :src="eds_base" name="eds"></iframe>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import Component from 'vue-class-component';

@Component({
  data() {
    return {
      edsMounted: false,
    };
  },
  mounted() {
    window.addEventListener('message', this.edsMessage, false);
  },
  beforeDestroy() {
    window.removeEventListener('message', this.edsMessage, false);
  },
  watch: {
    edsMounted() {
      if (this.edsMounted) {
        setTimeout(() => this.sendToken(), 500);
      }
    },
  },
})
export default class EDS extends Vue {
  edsMounted: boolean;

  eds_base = '/mainmenu/eds#signlist';

  get edsToken() {
    return this.$store.getters.user_data.eds_token;
  }

  edsMessage(e) {
    if (e?.data?.event === 'eds:mounted') {
      this.edsMounted = true;
    }
  }

  sendToken() {
    window.frames.eds.passEvent('set-token', this.edsToken);
  }
}
</script>

<style lang="scss" scoped>
.root {
  position: absolute;
  top: 36px;
  left: 0;
  right: 0;
  bottom: 0;

  overflow-x: hidden;
  overflow-y: hidden;

  iframe {
    display: block;
    width: 100%;
    height: 100%;
    border: none;
  }
}
</style>
