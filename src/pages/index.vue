<template>
  <div class="" id="container">
    <div>
      <Brander />
    </div>

    <div id="content">

      <div id="list">
        <div id="input_wrapper">
          <input type="number" id="add_item" v-model.number="newItem" placeholder="Nuevo gasto" />
          <button @click="addItem(1)">abel</button>
          <button @click="addItem(2)">bonzo</button>
        </div>

        <div id="item_list">
          <div v-for="item in sortedItems" :key="item.id" class="item">
            <span>{{ item.value }} {{ item.type }}</span>
            <button @click="deleteItem(item.id)">x</button>
          </div>
        </div>
      </div>

      <div id="total_wrapper">
        <div id="type_totals">
          <div class="type_totals_wrappers" id="abel_total">
            <label for="total" style="font-weight: normal;">gastos de abel</label>
            <input id="totalBonzo" type="text" class="total_input" :value="totalAbelPercentage" disabled />
          </div>

          <div class="type_totals_wrappers" id="bonzo_total">
            <label for="totalBonzo">gastos de bonzo</label>
            <input id="totalBonzo" type="text" class="total_input" :value="totalBonzoPercentage" disabled />
          </div>
        </div>

        <div class="type_totals_wrappers">
          <label for="totalBonzo">gastos totales</label>
          <input type="number" class="total_input" id="max_total" v-model="totalMax" disabled />
        </div>
      </div>

      <div id="deb_wrapper">
          <p v-if="calculateDebt().abel > 0">Abel debe pagarle a Bonzo: {{ calculateDebt().abel }}</p>
          <p v-if="calculateDebt().bonzo > 0">Abel debe pagarle a Bonzo: {{ calculateDebt().bonzo }}</p>
      </div>



      <div id="saving_wrapper">
        <input type="text" class="" id="phone_number" v-model="phoneNumber" placeholder="+543413690080"/>
        <button @click="prepareWhatsAppMessage">Enviar datos por WhatsApp</button>
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
      total: 0,
      totalBonzo: 0,
      totalMax: 0,
      bonzoPercentage: 50,
      abelPercentage: 50,
      wsp_content: "Vacío",
      sortedItems: [],
      phoneNumber: "+543413690080",
      abel: 0,
      bonzo: 0
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
        } else {
          this.itemsBonzo.push(item);
        }
      });

      this.abelSum();
      this.bonzoSum();
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
    maxSum(){
      const abelTotal = this.items.reduce((total, item) => total + parseInt(item.value), 0);
      const bonzoTotal = this.itemsBonzo.reduce((total, item) => total + parseInt(item.value), 0);
      return this.totalMax = abelTotal + bonzoTotal;
    },
    addItem(cateogry) {

      if(cateogry == 1){ // abel
        if (this.newItem !== null) {
          this.items.push({ id: Date.now(), value: this.newItem, type: "abel" });
          this.newItem = null;
          this.abelSum();

        }
      } else { // Variavles
        if (this.newItem !== null) {
          this.itemsBonzo.push({ id: Date.now(), value: this.newItem, type: "bonzo" });
          this.newItem = null;
          this.bonzoSum();
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
     

      this.abelSum();
      this.bonzoSum();

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
      if (this.total !== null && this.totalBonzo !== null) {
        const abelOwes = (this.totalBonzo - this.total) / 2;
        const bonzoOwes = (this.total - this.totalBonzo) / 2;

        if(abelOwes > bonzoOwes){
          return { abel: abelOwes.toFixed(0) };
        } else {
          return { bonzo: bonzoOwes.toFixed(0) };
        }

      }
      return null;
    }
  },
  computed: {
    sortedItems() {
      var allItems = [...this.items, ...this.itemsBonzo];
      return allItems.sort((a, b) => a.id - b.id);
    },
    totalAbelPercentage() {
      this.abelPercentage = Math.round(100 / this.totalMax * this.total);
      return `${this.total}`;
    },
    totalBonzoPercentage() {
      this.bonzoPercentage = Math.round(100 / this.totalMax * this.totalBonzo);
      return `${this.totalBonzo}`;
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

.item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 40px;
  padding: 0 10px;
  border-bottom: 1px solid #ccc;
}

#total_wrapper {
  margin-top: 20px;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
  background-color: #fff;
  color: #333;
}

.total_input {
  width: 100px;
  height: 40px;
  padding: 0 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
}

#type_totals {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.type_totals_wrappers {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  line-height: 40px;
  font-size: 16px;
}

.type_totals_wrappers .total_input{
  border: none;
  background-color: inherit;
}

@media (max-width: 480px) {
  #input_wrapper {
    width: 90%;
  }
  
  #item_list {
    width: 90%;
  }
  
  #total_wrapper {
    width: 90%;
  }
}


@media (max-width: 480px) {
  #container {
    display: flex;
    flex-direction: column;
    margin-top: unset;
  }
}
</style>