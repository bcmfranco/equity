<template>
  <div class="" id="container">
    <div id="brander">
      <Brander />
    </div>

    <div id="content">

      <div id="list">
        <div id="input_wrapper">
          <input type="number" id="add_item" v-model.number="newItem" placeholder="Nuevo gasto" />
          <input type="text" id="responsable" v-model.text="responsable" placeholder="Responsable" />
          <button @click="addItem2(newItem, responsable)">Agregar</button>
        </div>


        <div id="item_list">
          <div v-for="item in sortedItems" :key="item.id" class="item">
            <span>{{ item.value }} {{ item.type }}</span>
            <button @click="deleteItem(item.id)">x</button>
          </div>
        </div>
      </div>

      <div id="sentence">
        <div id="total_wrapper">
          <div id="type_totals">
            <div class="type_totals_wrappers" id="abel_total">
              <label for="total" style="font-weight: normal;">gastos de abel</label>
              <input id="totalBonzo" type="text" class="total_input" :value='this.newResposibles["player1"][1]' disabled />
            </div>

            <div class="type_totals_wrappers" id="bonzo_total">
              <label for="totalBonzo">gastos de bonzo</label>
              <input id="totalBonzo" type="text" class="total_input" :value="totalBonzoPercentage" disabled />
            </div>

            <div class="type_totals_wrappers" id="carlos_total">
              <label for="totalCarlos">gastos de carlos</label>
              <input id="totalCarlos" type="text" class="total_input" :value="totalCarlosPercentage" disabled />
            </div>

            <div class="type_totals_wrappers" id="daniel_total">
              <label for="totalDaniel">gastos de daniel</label>
              <input id="totalDaniel" type="text" class="total_input" :value="totalDanielPercentage" disabled />
            </div>

            <div class="type_totals_wrappers" id="enzo_total">
              <label for="totalEnzo">gastos de enzo</label>
              <input id="totalEnzo" type="text" class="total_input" :value="totalEnzoPercentage" disabled />
            </div>
          </div>

          <div class="type_totals_wrappers">
            <label for="totalBonzo">gasto total</label>
            <input type="number" class="total_input" id="max_total" v-model="totalMax" disabled />
          </div>
        </div>

        <div id="deb_wrapper">
          <p v-if="calculateDebt().abel > 0">de Abel a Bonzo: {{ calculateDebt().abel }}</p>
          <p v-if="calculateDebt().bonzo > 0">de Bonzo a Abel: {{ calculateDebt().bonzo }}</p>
          <p v-if="calculateDebt().carlos > 0">de Carlos a Daniel: {{ calculateDebt().carlos }}</p>
          <p v-if="calculateDebt().daniel > 0">de Daniel a Enzo: {{ calculateDebt().daniel }}</p>
          <p v-if="calculateDebt().enzo > 0">de Enzo a Abel: {{ calculateDebt().enzo }}</p>
        </div>

      </div>

      <!-- <div id="saving">
        <input type="text" class="" id="phone_number" v-model="phoneNumber" placeholder="+543413690080"/>
        <button @click="prepareWhatsAppMessage">Enviar datos por WhatsApp</button>
      </div>       -->

    </div>

    <div>
      <Footer />
    </div>
  </div>
</template>

<script>
import Brander from '../../public/components/brander.vue';
import Footer from '../../public/components/footer.vue';

// Hay que revisar las cosas que se están mandando por wsp

export default {
  components: {
    Brander,
    Footer
  },
  data() {
    return {
      newItem: null,
      items: [],
      itemsBonzo: [],
      itemsCarlos: [],
      itemsDaniel: [],
      itemsEnzo: [],
      total: 0,
      totalBonzo: 0,
      totalCarlos: 0,
      totalDaniel: 0,
      totalEnzo: 0,
      totalMax: 0,
      abelPercentage: 20,
      bonzoPercentage: 20,
      carlosPercentage: 20,
      danielPercentage: 20,
      enzoPercentage: 20,
      wsp_content: "Vacío",
      sortedItems: [],
      phoneNumber: "+543413690080",
      abel: 0,
      bonzo: 0,
      newItemValue: null,
      newItemResponsible: null,
      newResposibles: {"player1": ["abel", 0], "player2": ["bonzo", 0], "player3": ["carlos", 0], "player4": ["daniel", 0], "player5": ["enzo", 0]},
      lastPlayerEdited: 0
    };
  },
  mounted() {

    // Read token and write items
    var token = window.location.search.substring(1);
    if(token){
      var parts = token.split("zzz");
      var allItems = parts.map(part => {
        var [id, value, type] = part.split(",");
        return { id: parseInt(id), value: parseInt(value), type: type === "f" ? "abel" : "bonzo" };
      });

      allItems.forEach(item => {
        if(item.type === "abel") {
          this.items.push(item);
        } else if (item.type === "bonzo") {
          this.itemsBonzo.push(item);
        } else if (item.type === "carlos") {
          this.itemsCarlos.push(item);
        } else if (item.type === "daniel") {
          this.itemsDaniel.push(item);
        } else {
          this.itemsEnzo.push(item);
        }
      });

      this.abelSum();
      this.bonzoSum();
      this.carlosSum();
      this.danielSum();
      this.enzoSum();
      this.maxSum();

    }
    ////////////////
  },
  methods: {
    abelSum(){
      return this.total = this.items.reduce((total, item) => total + parseInt(item.value), 0);
    },
    bonzoSum(){
      return this.totalBonzo = this.itemsBonzo.reduce((total, item) => total + parseInt(item.value), 0);
    },
    carlosSum(){
      return this.totalBonzo = this.itemsCarlos.reduce((total, item) => total + parseInt(item.value), 0);
    },
    danielSum(){
      return this.totalBonzo = this.itemsDaniel.reduce((total, item) => total + parseInt(item.value), 0);
    },
    enzoSum(){
      return this.totalBonzo = this.itemsEnzo.reduce((total, item) => total + parseInt(item.value), 0);
    },
    maxSum(){
      const abelTotal = this.items.reduce((total, item) => total + parseInt(item.value), 0);
      const bonzoTotal = this.itemsBonzo.reduce((total, item) => total + parseInt(item.value), 0);
      const carlosTotal = this.itemsCarlos.reduce((total, item) => total + parseInt(item.value), 0);
      const danielTotal = this.itemsDaniel.reduce((total, item) => total + parseInt(item.value), 0);
      const enzoTotal = this.itemsEnzo.reduce((total, item) => total + parseInt(item.value), 0);

      return this.totalMax = abelTotal + bonzoTotal + carlosTotal + danielTotal + enzoTotal;
    },
    addItem2(newItem, responsible) {

      // Hay que probarlo con varios players

      var existingMember = false;

      for (let key in this.newResposibles) {
        if (this.newResposibles.hasOwnProperty(key)) {
          if (this.newResposibles[key][0] === responsible) { 
            existingMember = true;
            var existingMemberKey = key;
          }
        }
      }

      if(existingMember){ // Player existente
        this.newResposibles[existingMemberKey] = [responsible, newItem];
      } else { // Nuevo player

        if(this.lastPlayerEdited < 6){
          this.lastPlayerEdited ++;
          var playerToEdit = "player"+this.lastPlayerEdited.toString();
          this.newResposibles[playerToEdit] = [responsible, newItem];
        }

        // Hay que hacer que estos valores se mapeen de forma reactiva

        console.log(this.newResposibles["player1"]);
      }

    },

    addItem(cateogry) {

      if(cateogry == 1){ // abel
        if (this.newItem !== null) {
          this.items.push({ id: Date.now(), value: this.newItem, type: "abel" });
          this.newItem = null;
          this.abelSum();

        }
      } else if (cateogry == 2) { // bonzo
        if (this.newItem !== null) {
          this.itemsBonzo.push({ id: Date.now(), value: this.newItem, type: "bonzo" });
          this.newItem = null;
          this.bonzoSum();
        }
      } else if (cateogry == 3) { // carlos
        if (this.newItem !== null) {
          this.itemsCarlos.push({ id: Date.now(), value: this.newItem, type: "carlos" });
          this.newItem = null;
          this.carlosSum();
        }
      } else if (cateogry == 4) { // daniel
        if (this.newItem !== null) {
          this.itemsDaniel.push({ id: Date.now(), value: this.newItem, type: "daniel" });
          this.newItem = null;
          this.danielSum();
        }
      } else { // enzo
        if (this.newItem !== null) {
          this.itemsEnzo.push({ id: Date.now(), value: this.newItem, type: "enzo" });
          this.newItem = null;
          this.enzoSum();
        }
      }

      this.maxSum();
    },
    deleteItem(id) {

      let index = this.items.findIndex(item => item.id === id);
      if (index !== -1) {
        this.total -= parseInt(this.items[index].value);
        this.totalMax -= parseInt(this.items[index].value);
        this.items.splice(index, 1);
      }

      index = this.itemsBonzo.findIndex(item => item.id === id);
      if (index !== -1) {
        this.totalBonzo -= parseInt(this.itemsBonzo[index].value);
        this.totalMax -= parseInt(this.itemsBonzo[index].value);
        this.itemsBonzo.splice(index, 1);
      }

      index = this.itemsCarlos.findIndex(item => item.id === id);
      if (index !== -1) {
        this.totalCarlos -= parseInt(this.itemsCarlos[index].value);
        this.totalMax -= parseInt(this.itemsCarlos[index].value);
        this.itemsCarlos.splice(index, 1);
      }

      index = this.itemsDaniel.findIndex(item => item.id === id);
      if (index !== -1) {
        this.totalDaniel -= parseInt(this.itemsDaniel[index].value);
        this.totalMax -= parseInt(this.itemsDaniel[index].value);
        this.itemsDaniel.splice(index, 1);
      }

      index = this.itemsEnzo.findIndex(item => item.id === id);
      if (index !== -1) {
        this.totalBonzo -= parseInt(this.itemsEnzo[index].value);
        this.totalMax -= parseInt(this.itemsEnzo[index].value);
        this.itemsEnzo.splice(index, 1);
      }
     
      this.abelSum();
      this.bonzoSum();
      this.carlosSum();
      this.danielSum();
      this.enzoSum();
    },
    prepareWhatsAppMessage() {
      // Genero token
      if(this.items[0] || this.itemsBonzo[0]){
        var allItems = [...this.items, ...this.itemsBonzo].sort((a, b) => a.id - b.id);
        
        var itemJoined = allItems.map(item => {
          return item.id + "," + item.value + "," + (item.type === "abel" ? "f" : "v");
        }).join("zzz");
      }

      // Obtener el número de teléfono del input
      this.phone_number = this.phoneNumber;

      // Paso el token como contenido de wsp
      this.wsp_content = itemJoined;
      window.location.href = 'https://api.whatsapp.com/send?phone=' + this.phone_number +'&text=' + window.location.href + "?" + encodeURIComponent(this.wsp_content);
    },
    calculateDebt() {
      if (this.total !== null && this.totalBonzo !== null && this.totalCarlos !== null && this.totalDaniel !== null && this.totalEnzo !== null) {
        const totalGasto = this.total + this.totalBonzo + this.totalCarlos + this.totalDaniel + this.totalEnzo;
        const pagoPorPersona = totalGasto / 5;

        let debt = {};
        if (this.total < pagoPorPersona) {
          debt.abel = (pagoPorPersona - this.total).toFixed(0);
        } else {
          debt.bonzo = (this.total - pagoPorPersona).toFixed(0);
        }

        if (this.totalBonzo < pagoPorPersona) {
          debt.bonzo = (pagoPorPersona - this.totalBonzo).toFixed(0);
        } else {
          debt.carlos = (this.totalBonzo - pagoPorPersona).toFixed(0);
        }

        if (this.totalCarlos < pagoPorPersona) {
          debt.carlos = (pagoPorPersona - this.totalCarlos).toFixed(0);
        } else {
          debt.daniel = (this.totalCarlos - pagoPorPersona).toFixed(0);
        }

        if (this.totalDaniel < pagoPorPersona) {
          debt.daniel = (pagoPorPersona - this.totalDaniel).toFixed(0);
        } else {
          debt.enzo = (this.totalDaniel - pagoPorPersona).toFixed(0);
        }

        if (this.totalEnzo < pagoPorPersona) {
          debt.enzo = (pagoPorPersona - this.totalEnzo).toFixed(0);
        } else {
          debt.abel = (this.totalEnzo - pagoPorPersona).toFixed(0);
        }

        return debt;
      }
      return null;
    }
  },
  computed: {
    sortedItems() {
      var allItems = [...this.items, ...this.itemsBonzo, ...this.itemsCarlos, ...this.itemsDaniel, ...this.itemsEnzo];
      return allItems.sort((a, b) => a.id - b.id);
    },
    totalAbelPercentage() {
      return `${this.newResposibles["player1"][1]}`
    },
    totalBonzoPercentage() {
      this.bonzoPercentage = Math.round(100 / this.totalMax * this.totalBonzo);
      return `${this.totalBonzo}`;
    },
    totalCarlosPercentage() {
      this.carlosPercentage = Math.round(100 / this.totalMax * this.totalCarlos);
      return `${this.totalCarlos}`;
    },
    totalDanielPercentage() {
      this.danielPercentage = Math.round(100 / this.totalMax * this.totalDaniel);
      return `${this.totalDaniel}`;
    },
    totalEnzoPercentage() {
      this.enzoPercentage = Math.round(100 / this.totalMax * this.totalEnzo);
      return `${this.totalEnzo}`;
    }
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

#content {
  display: flex;
  flex-direction: column;
  align-items: center;
}

#content #list{
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

#input_wrapper button {
  height: 40px;
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

#input_wrapper button:hover {
  background-color: #0056b3;
}

#item_list {
  width: 300px;
  margin-top: 20px;
  padding: 20px;
  color: #333;
}

#item_list .item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 40px;
  border-bottom: 1px solid #ccc;
  color: #333;
}

#item_list button {
  border: none;
  background-color: inherit;
  font-size: 14px;
}

#sentence{
  border-radius: 10px;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
  background-color: #fff;
  margin: 10px 0px;
  width: 100%;
  color: #333;
  padding: 10px 20px;
  width: 300px;
}

#sentence #total_wrapper {
  margin-top: 20px;
  border-bottom: 1px solid #ccc;
}

#total_wrapper #type_totals{
  border-bottom: 1px solid lightgray;
}

.total_input {
  width: 100px;
  height: 30px;
  padding: 0 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
}

#type_totals {
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