<template>
  <div class="switcher" @click="toggleSwitch" :class="{ 'paper_type': paperType }">
    <div class="switcher-button"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      paperType: false
    };
  },
  methods: {
    toggleSwitch() {
      this.paperType = !this.paperType;
      this.$emit('change', this.paperType);
    }
  }
};
</script>

<style scoped>
.switcher {
  width: 60px;
  height: 30px;
  background-color: #ccc;
  border-radius: 15px;
  position: relative;
  cursor: pointer;
}

.switcher.active {
  background-color: #ccc;
}

.switcher-button {
  width: 30px;
  height: 30px;
  background-color: #fff;
  border-radius: 15px;
  position: absolute;
  top: 0;
  left: 0;
  transition: transform 0.3s;
}

.switcher.paper_type .switcher-button {
  transform: translateX(30px);
}
</style>
