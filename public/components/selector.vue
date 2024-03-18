<template>
    <div id="button_wrapper">
      <button
        :class="{ active: selected === 'fixed' }"
        @click.prevent="select('fixed')"
      >
        Fijo
      </button>
      <button
        :class="{ active: selected === 'variable' }"
        @click.prevent="select('variable')"
      >
        Variable
      </button>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        selected: null,
      };
    },
    methods: {
      select(option) {
        if (this.selected === option) {
          this.selected = null;
        } else {
          this.selected = option;
        }
      },
    },
  };
  </script>
  
  <style scoped>

  /* Va a reuqerir mucha adaptación a mobile */

  #button_wrapper{
    display: flex;
    flex-direction: row;
    gap: 10px;
  }

  button {
    width: 100px;
    padding: 10px 20px;
    border: none;
    cursor: pointer;
    transition: background-color 0.3s;
  }
  
  .active {
    background-color: #C4368C;
    color: white;
  }
  </style>
  