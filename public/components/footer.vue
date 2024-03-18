<template>
    <a id="gum_footer" href="https://gumball-xi.vercel.app/">
        powered by
        <span id="logotype">GU</span>
        gumball
    </a>
  </template>
  
  <style scoped>

    @import url('https://fonts.googleapis.com/css2?family=Cabin:ital,wght@0,400..700;1,400..700&family=Inter:wght@100..900&display=swap');
  
    #gum_footer {
        position: fixed;
        left: 0;
        bottom: 0;
        width: 100%;
        height: 10px;
        padding: 20px 0;
        background-color: rgba(255, 255, 255, 0.2);
        text-align: center;
        line-height: 10px;
        color: #dedede;
        font-family: Cabin;
        z-index: 1000;
    }

    #gum_footer #logotype{
        color: #dedede;
        width: 20px;
        height: 20px;
        border: 1px solid #c4c4c4;
        border-radius: 2px;
        padding: 0px;
    }

  
    @media (max-width: 480px) {
      a {
        font-size: 14px;
        padding: 8px;
      }
    }
  
  </style>
  