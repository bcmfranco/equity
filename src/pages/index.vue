<template>
  <div class="" id="container">
    <div id="brander">
      <Brander />
    </div>

    <div ref="wizzard1" class="wizzard">

      <div id="list">
        <div id="input_wrapper">
          <input type="number" id="add_item" v-model.number="newItem" placeholder="Nuevo gasto" />
          <input type="text" id="responsable" v-model.text="responsable" placeholder="Responsable" />
          <button class="go_button" @click="addItem2(newItem, responsable)">Agregar</button>
        </div>
      </div>

      <div class="sentence">
        <div id="total_wrapper">
          <div class="type_totals">
            <div class="type_totals_wrappers" v-if="this.newResposibles.player1[1] > 0">
              <label for="total" style="font-weight: normal;">{{ this.newResposibles["player1"][0] }}</label>
              <input class="total_input" :value='this.newResposibles["player1"][1]' disabled />
            </div>

            <div class="type_totals_wrappers" v-if="this.newResposibles.player2[1] > 0">
              <label for="total" style="font-weight: normal;">{{ this.newResposibles["player2"][0] }}</label>
              <input class="total_input" :value='this.newResposibles["player2"][1]' disabled />
            </div>

            <div class="type_totals_wrappers" v-if="this.newResposibles.player3[1] > 0">
              <label for="total" style="font-weight: normal;">{{ this.newResposibles["player3"][0] }}</label>
              <input class="total_input" :value='this.newResposibles["player3"][1]' disabled />
            </div>

            <div class="type_totals_wrappers" v-if="this.newResposibles.player4[1] > 0">
              <label for="total" style="font-weight: normal;">{{ this.newResposibles["player4"][0] }}</label>
              <input class="total_input" :value='this.newResposibles["player4"][1]' disabled />
            </div>

            <div class="type_totals_wrappers" v-if="this.newResposibles.player5[1] > 0">
              <label for="total" style="font-weight: normal;">{{ this.newResposibles["player5"][0] }}</label>
              <input class="total_input" :value='this.newResposibles["player5"][1]' disabled />
            </div>
          </div>

          <div class="type_totals_wrappers">
            <label for="totalBonzo">gasto total</label>
            <input type="number" class="total_input" id="max_total" v-model="totalPlayerSpendt" disabled />
          </div>
        </div>
      </div>

      <div class="btn_wrapper">
        <button class="go_button" @click="calculate2()">Calcular</button>
      </div>

    </div>

    <div ref="wizzard2" class="wizzard" wizzard="2" style="display: none;">

      <div class="sentence">
        <div class="type_totals_wrappers">
            <label for="totalBonzo">gasto total</label>
            <input type="number" class="total_input" id="max_total" v-model="totalPlayerSpendt" disabled />
          </div>
      </div>
    
      <div class="sentence">
        <div class="debt_totals">
            <div class="type_totals_wrappers" id="abel_total" v-if="this.newResposibles.player1[2] > 0">
              <span>{{ this.newResposibles["player1"][0] }}</span>
              <span>{{ this.newResposibles["player1"][3] }}</span>
            </div>

            <div class="type_totals_wrappers" id="abel_total" v-if="this.newResposibles.player2[2] > 0">
              <span>{{ this.newResposibles["player2"][0] }}</span>
              <span>{{ this.newResposibles["player2"][3] }}</span>
            </div>

            <div class="type_totals_wrappers" id="abel_total" v-if="this.newResposibles.player3[2] > 0">
              <span>{{ this.newResposibles["player3"][0] }}</span>
              <span>{{ this.newResposibles["player3"][3] }}</span>
            </div>

            <div class="type_totals_wrappers" id="abel_total" v-if="this.newResposibles.player4[2] > 0">
              <span>{{ this.newResposibles["player4"][0] }}</span>
              <span>{{ this.newResposibles["player4"][3] }}</span>
            </div>

            <div class="type_totals_wrappers" id="abel_total" v-if="this.newResposibles.player5[2] > 0">
              <span>{{ this.newResposibles["player5"][0] }}</span>
              <span>{{ this.newResposibles["player5"][3] }}</span>
            </div>
          </div>

      </div>

      <div class="btn_wrapper">
        <button class="go_button" @click="back()">Volver</button>
      </div>
    
    </div>  

    <div>
      <Footer />
    </div>
  </div>
</template>

<script>
import Brander from '../../public/components/brander.vue';
import Footer from '../../public/components/footer.vue';

export default {
  components: {
    Brander,
    Footer
  },
  data() {
    return {
      newItem: null,
      newResposibles: {"player1": ["player1", null], "player2": ["player2", null], "player3": ["player3", null], "player4": ["player4", null], "player5": ["player5", null]},
      lastPlayerEdited: 0,
      spendingObj: {},
      playerDebts: {}
    };
  },
  mounted() {

  },
  methods: {
    totalSum(){ // Devuelve la suma de todos los gastos de todos los players

      this.totalPlayerSpendt = 0;
      for (let key in this.newResposibles) {
        this.totalPlayerSpendt += this.newResposibles[key][1];
      }

      return this.totalPlayerSpendt;
    },
    addItem2(newItem, responsible) { // Agrega un gasto a un player

      var existingMember = false;

      for (let key in this.newResposibles) {
        if (this.newResposibles.hasOwnProperty(key)) {
          if (this.newResposibles[key][0] === responsible) { 
            existingMember = true;
            var existingMemberKey = key;
          }
        }
      }

      this.lastPlayerEdited++;

      if (existingMember) {
        this.newResposibles[existingMemberKey][1] += newItem;
      } else { // Nuevo player
        if (this.lastPlayerEdited < 6) {
          var playerToEdit = "player" + this.lastPlayerEdited.toString();
          this.newResposibles[playerToEdit] = [responsible, newItem];
        }
      }
      
      this.totalSum(); // Llamo al método que suma el total
      
    },
    calculate2(){
      if(this.totalPlayerSpendt > 0){
        var averageSpent = this.totalPlayerSpendt / this.lastPlayerEdited;

        // Calcula las deudas individuales de cada jugador
        for (let key in this.newResposibles) {
          if(this.newResposibles[key][1] != null){
            var individualPlayerSpent = this.newResposibles[key][1];
            var debt = averageSpent - individualPlayerSpent;
            this.newResposibles[key][2] = debt;
            this.playerDebts[key + 'Debt'] = debt;
          }
        }

        // Realiza las transacciones
        for (let key in this.newResposibles) {
          if(this.newResposibles[key][1] != null){
            var playerDebt = this.playerDebts[key + 'Debt'];

            // Busca a quién le debe pagar el jugador actual
            for (let otherKey in this.newResposibles) {
              if (key !== otherKey) {
                var otherPlayerDebt = this.playerDebts[otherKey + 'Debt'];

                // Si el jugador debe pagar y el otro jugador debe recibir
                if (playerDebt > 0 && otherPlayerDebt < 0) {
                  var amountToPay = Math.min(-otherPlayerDebt, playerDebt);
                  var payer_name = this.newResposibles[key][0];
                  var reciber_name = this.newResposibles[otherKey][0];

                  this.newResposibles[key][3] = `paga ${amountToPay} a ${reciber_name}`;
                  this.newResposibles[otherKey][3] = `recibe ${amountToPay} de ${payer_name}`;

                  console.log(`${payer_name} paga ${amountToPay} a ${reciber_name}`);
                }
              }
            }
          }
        }

        // Muestra el wizzard 2 y oculta el 1
        this.$refs.wizzard1.style.display = 'none';
        this.$refs.wizzard2.style.display = 'block';

        return this.newResposibles;
      }
    },

    back(){ // Muestro el wizzard 1 y oculto el 2
        this.$refs.wizzard1.style.display = 'block';
        this.$refs.wizzard2.style.display = 'none';
    },

    listPayers(){ // Listo los players que tienen deuda
      for (let key in this.newResposibles) {
        if(this.newResposibles[key][2] > 0){
          console.log(this.newResposibles[key]);
        }
      }
    },

  },
  computed: {

  }
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@400;700&display=swap');

#container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  font-family: "Montserrat", sans-serif;
  background-color: #f5f5f5;
}

#container #brander{
  width: 50%;
}

.wizzard {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.wizzard #list{
  width: 100%;
  border-radius: 10px;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
  background-color: #fff;
}

#input_wrapper {
  display: flex;
  flex-direction: column;
  gap: 10px;
  width: 300px;
  padding: 20px;
}

#input_wrapper input {
  height: 40px;
  padding: 0 10px;
  font-size: 16px;
  border-radius: 5px;
  border: 1px solid lightgrey;
}

.go_button {
  height: 40px;
  width: 100%;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: #fff;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
  text-align: left;
  text-indent: 5px;
}

.go_button:hover {
  background-color: #0056b3;
}

.sentence{
  border-radius: 10px;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
  background-color: #fff;
  margin: 10px 0px;
  width: 100%;
  color: #333;
  padding: 10px 20px;
  width: 300px;
}

.sentence #total_wrapper {
  margin-top: 20px;
  border-bottom: 1px solid #ccc;
}

#total_wrapper .type_totals{
  border-bottom: 1px solid lightgray;
}

.btn_wrapper{
  display: flex;
  justify-content: center;
  border-radius: 10px;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
  background-color: #fff;
  margin: 10px 0px;
  width: 100%;
  color: #333;
  padding: 10px 20px;
  width: 300px;
}

.total_input {
  width: 100px;
  height: 30px;
  padding: 0 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
}

.type_totals {
  display: flex;
  flex-direction: column;
}

.type_totals_wrappers {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  line-height: 30px;
  font-size: 16px;
}

.type_totals_wrappers .total_input{
  border: none;
  background-color: inherit;
}

.debt_totals .type_totals_wrappers{
  display: grid;
  grid-template-columns: 25% auto;
  padding: 0px 10px;
}

#saving{
  display: flex;
  flex-direction: column;
  gap: 5px;
  border-radius: 10px;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
  background-color: #fff;
  margin: 10px 0px;
  width: 100%;
  color: #333;
  padding: 10px 20px;
  width: 300px;
}

#saving input {
  height: 40px;
  padding: 0 10px;
  font-size: 16px;
  border-radius: 5px;
  border: 1px solid lightgrey;
  color: #333;
}

#saving button {
  height: 40px;
  border-radius: 5px;
  border: 1px solid lightgrey;
}

@media (max-width: 480px) {

  #container {
    display: flex;
    flex-direction: column;
    margin-top: 0;
    height: 100vh;
  }

  #container #brander {
    width: 300px;
    position: absolute;
    top: 0px;
  }
}

</style>