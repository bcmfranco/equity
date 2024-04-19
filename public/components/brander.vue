<template>

  <div id="gumcom_brander">
    <div id="logotype">
      <div>PO</div>
    </div>
    <div id="isotype">
      <h1>poniendo estaba la gansa</h1>
      <h2>dividí gastos sin descargar una app</h2>
    </div>
  </div>


</template>


<style scoped>

  @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

  #gumcom_brander{
    font-family: Roboto;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-items: center;
    margin: 10px 0px;
  }

  #gumcom_brander #logotype{
    font-size: 25px;
    color: #484848;
    width: 50px; /* Ajusta el ancho según tus necesidades */
    height: 50px; /* Ajusta la altura según tus necesidades */
    border: 2px solid #484848; /* Color del borde */
    border-radius: 8px; /* Ajusta el radio del borde según tus necesidades */
    box-shadow: 5px 5px 10px rgba(0, 0, 0, 0.1); /* Añade una sombra */
    margin: 10px;
  }

  #gumcom_brander #isotype h1 {
    color: #484848;
    margin: 0px;
    padding: 0px;
  }
  
  #gumcom_brander #isotype h2 {
    font-size: 24px;
    color: #484848;
    margin-bottom: 20px;
    margin: 0px;
    padding: 0px;
  }


  @media (max-width: 480px) {

    #gumcom_brander{
      flex-direction: column;
      align-items: start;
    }


  }




</style>