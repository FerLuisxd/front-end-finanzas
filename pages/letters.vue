<template>
  <div class="e-nuxt-container">
    <h2>Bienvenidos</h2>

      <v-layout align-start>
        <v-flex>
          <v-toolbar flat color="white">
            <v-toolbar-title>Letras</v-toolbar-title>
            <v-divider class="mx-2" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-text-field
              class="text-xs-center"
              v-model="search"
              append-icon="mdi-anchor"
              label="Buscar"
              single-line
              hide-details
              color="green"
            ></v-text-field>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <template v-slot:activator="{ on }">
                <v-btn v-on="on" color="green" dark class="mb-2">Nuevo</v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="headline">{{ formTitle2 }}</span>
                </v-card-title>
                <v-card-text>
                  <v-container grid-list-md>
                    <v-layout wrap>
                      <v-flex xs12 sm12 md12>
                        <v-btn color="green" text @click="dialogTN=true">Nominal Tax</v-btn>
                        <v-dialog v-model="dialogTN" max-width="500px">
                          <v-card>
                            <v-card-title>
                              <span class="headline">{{ formTitle3 }}</span>
                            </v-card-title>
                            <v-card-text>
                              <v-container grid-list-md>
                                <v-layout wrap>
                                  <v-flex xs12 sm12 md12>
                                    <v-text-field
                                      v-model="cap"
                                      label="Capitalization"
                                      type="number"
                                    ></v-text-field>
                                  </v-flex>
                                  <v-flex xs12 sm12 md12>
                                    <v-text-field
                                      v-model="taxType"
                                      label="Type (days)"
                                      type="number"
                                    ></v-text-field>
                                  </v-flex>
                                  <v-flex xs12 sm12 md12>
                                    <v-radio-group v-model="daysOf" row>
                                      <v-radio label="360 days" value="360"></v-radio>
                                      <v-radio label="365 days" value="365"></v-radio>
                                    </v-radio-group>
                                  </v-flex>
                                  <v-flex xs12 sm12 md12>
                                    <v-text-field v-model="nomTax" label="Tax" type="number"></v-text-field>
                                  </v-flex>
                                </v-layout>
                              </v-container>
                            </v-card-text>

                            <v-card-actions>
                              <v-spacer></v-spacer>
                              <v-btn color="green" text @click.native="closeTax">Cancel</v-btn>
                              <v-btn color="green" text @click.native="addTax">Save</v-btn>
                            </v-card-actions>
                          </v-card>
                        </v-dialog>
                        <v-text-field v-model="tea" label="TEA %" type="number"></v-text-field>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-date-picker
                          v-model="dateEnd"
                          class="mt-4"
                          :min="startDateLet"
                          :max="endDateLet"
                        ></v-date-picker>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-select v-model="buyerId" :items="buyers" label="Buyer"></v-select>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-btn color="white" class="mb-2" @click="newCost(item)">add Cost</v-btn>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-label>Startup Costs</v-label>
                        <v-list-item v-for="(item, i) in costsStart" :key="i">
                          <v-list-item-content v-if="item.start">
                            <v-list-item-title>{{item.name}}</v-list-item-title>
                            <v-list-item-subtitle>Valor {{item.amount}}</v-list-item-subtitle>
                          </v-list-item-content>
                        </v-list-item>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-label>Cancellation Costs</v-label>
                        <v-list-item v-for="(item, i) in costsEnd" :key="i">
                          <v-list-item-content>
                            <v-list-item-title>{{item.name}}</v-list-item-title>
                            <v-list-item-subtitle>Valor {{item.amount}}</v-list-item-subtitle>
                          </v-list-item-content>
                        </v-list-item>
                        <!-- <v-tab v-for="(item, i) in items" :key="i"> -->
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="green" text @click.native="closeEnd()">Cancel</v-btn>
                  <v-btn color="green" text @click="saveEnd()">Save</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            <v-dialog v-model="dialogCost" max-width="500px">
              <v-card>
                <v-card-title>
                  <span class="headline">{{ formTitle4 }}</span>
                </v-card-title>
                <v-card-text>
                  <v-container grid-list-md>
                    <v-layout wrap>
                      <v-flex xs12 sm12 md12>
                        <v-text-field v-model="costName" label="Name"></v-text-field>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-radio-group v-model="costStart" row>
                          <v-radio label="At the start" value="true"></v-radio>
                          <v-radio label="At the end" value="false"></v-radio>
                        </v-radio-group>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-radio-group v-model="costNumber" row>
                          <v-radio label="Value" value="true"></v-radio>
                          <v-radio label="Percentage" value="false"></v-radio>
                        </v-radio-group>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-text-field v-model="costValue" label="Value" type="number"></v-text-field>
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="green" text @click.native="closeCost">Cancel</v-btn>
                  <v-btn color="green" text @click.native="addCost">Save</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </template>
          <template v-slot:no-data>
            <v-btn color="primary" @click.native="initialize">Reset</v-btn>
          </template>
        </v-data-table>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
export default {
  data() {
    return {
      costName: "",
      costStart: true,
      costNumber: true,
      costValue: 0,
      buyerId: "",
      buyers: [],
      costs: [],

      externalContent: "",
      audio_stream: null,
      audio_context: null,
      recorder: null,
      loginLoading: false,
      email: "",
      password: "",
      letters: [],
      dialog: false,
      dialogEnd: false,
      dialogCost: false,
      headers: [
        { text: "Correlative", value: "correlative", sortable: false },
        { text: "Start date", value: "startDate", sortable: false },
        { text: "End date", value: "endDate", sortable: false }, ///CAMPOS
        {text: "Moneda", value:"moneyString", sortable:false},
        { text: "Nominal Value", value: "nominalValue", sortable: false }, ///CAMPOS
        { text: "Actions", value: "action", sortable: false }
      ],
      dates: [],
      clients: ["a", "b"],
      search: "",
      editedIndex: -1,
      id: "",
      userId: "",
      clientId: "",
      correlative: "",
      startDate: "",
      endDate: "",
      nominalValue: "",
      moneyTypes: ["Nuevo Sol", "Dolar americano"],
      moneyType: "",
      endorsmentId: "",
      editedIndex: -1,
      dateEnd: "",
      tea: "",
      startDateLet: "",
      endDateLet: "",
      letterId: "",

      taxType: "",
      cap: "",
      daysOf: "",
      nomTax: "",
      dialogTN: false
    };
  },
  created() {},
  methods: {
    ///TAX
    addTax() {
      console.log("hi");
      this.tea = this.calcTea(
        this.taxType,
        this.cap,
        Number(this.daysOf),
        this.nomTax
      );
      this.dialogTN = false;
    },
    closeTax() {
      (this.taxType = ""),
        (this.cap = ""),
        (this.daysOf = ""),
        (this.nomTax = "");
      this.dialogTN = false;
    },
    ///METODOS COST
    newCost(item) {
      this.dialogCost = true;
    },
    addCost(item) {
      console.log(this.nominalValue, this.costValue, this.costNumber);
      let value = this.nominalValue * (this.costValue / 100);
      // console.log("add",this.costNumber,value)
      if (this.costNumber == false || this.costNumber == "false") {
        this.costNumber = false;
        console.log("valor", value);
        this.costValue = value;
      }
      this.costStart == "true"
        ? (this.costStart = true)
        : (this.costStart = false);
      console.log(this.nominalValue, this.costValue);
      let cost = {
        name: this.costName,
        amount: Number(Number(this.costValue).toFixed(2)),
        start: this.costStart
      };
      this.costs.push(cost);
      this.cleanCost();
      this.dialogCost = false;
    },
    cleanCost() {
      (this.costName = ""),
        (this.costStart = true),
        (this.costNumber = true),
        (this.costValue = 0);
    },
    closeCost() {
      this.dialogCost = false;
    },
    deleteCost(item) {},

    close() {
      this.dialog = false;
      this.editedIndex = -1;
    },
    editItem(item) {
      this.id = item.id;
      this.name = item.name;
      this.description = item.description;
      this.command = item.command;
      this.usable = item.usable;
      this.shortcut = item.shortcut;
      this.keymap = item.keymap;
      this.location = item.location;
      this.editedIndex = 1;
      this.dialog = true;
    },
    save() {
      if (this.editedIndex > -1) {
        //Código para editar

        let me = this;
        console.log(me.id);
        me.$axios
          .put(`/command/${me.id}`, {
            command: {
              name: me.name,
              description: me.description,
              command: me.command,
              usable: me.usable,
              shortcut: me.shortcut,
              keymap: me.keymap,
              location: me.location
            },
            id: me.id
          })
          .then(function(response) {
            // console.log(response);
            if (response.data != true) {
            }
            me.close();
            me.list();
            me.clean();
          });
      } else {
        //Código para guardar
        let me = this;
        console.log(this.moneyType)
        if(this.moneyType==this.moneyTypes[0]){
          this.moneyType='sol'
        }
        else{
          this.moneyType='dol'
        }
        me.$axios
          .post("/letter", {
            correlative: me.correlative,
            user: { id: this.userID },
            client: { id: this.clientId },
            startDate: this.dates[0],
            endDate: this.dates[1],
            nominalValue: Number(me.nominalValue),
            moneyType: this.moneyType
            // endorsmentId: "",
          })
          .then(function(response) {
            // console.log(response);
            if (response.data != true) {
            }
            me.close();
            me.list();
            me.clean();
          });
      }
    },
    clean() {
      (this.clientId = ""),
        (this.correlative = ""),
        (this.startDate = ""),
        (this.endDate = ""),
        (this.nominalValue = ""),
        (this.moneyTypes = ["Nuevo Sol", "Dolar americano"]),
        (this.moneyType = "");
      (this.editedIndex = -1),
        (this.id = ""),
        (this.userId = ""),
        (this.clientId = ""),
        (this.correlative = ""),
        (this.startDate = ""),
        (this.endDate = ""),
        (this.nominalValue = ""),
        (this.moneyType = ""),
        (this.endorsmentId = ""),
        (this.editedIndex = -1),
        (this.dateEnd = ""),
        (this.tea = ""),
        (this.startDateLet = ""),
        (this.endDateLet = "");
      this.costs = [];
    },
    newEndorsement(item) {
      console.log("before", this.clients);
      this.dialogEnd = true;
      this.startDateLet = item.startDate;
      this.endDateLet = item.endDate;
      this.letterId = item.id;
      this.nominalValue = item.nominalValue;
      // this.listClients()
      console.log("after", this.clients);
      console.log(item);
      // item.startDate= ''
      console.log(item.startDate, item.endDate);
    },
    list() {
      this.listLetters();
      this.listClients();
      this.listBuyers();
    },
    listBuyers() {
      let me = this;
      me.buyers = [];
      me.$axios.get(`/buyer`).then(function(response) {
        let arrayRes = response.data;
        arrayRes.map(p => {
          me.buyers.push({
            text: p.comercialName,
            value: p.id
          });
        });
      });
    },
    listLetters() {
      let me = this;
      me.letters = [];
      me.$axios.get(`/letter/${this.userID}`).then(function(response) {
        let arrayLetters = response.data;
        for (let i = 0; i < arrayLetters.length; i++) {
          if(arrayLetters[i].moneyType=='sol'){
            arrayLetters[i].moneyString = 'S/'
          }
          else{
            arrayLetters[i].moneyString = '$'
          }
          arrayLetters[i].startDate = me
            .$moment(arrayLetters[i].startDate, "YYYY-MM-DD")
            .format("YYYY-MM-DD");
          arrayLetters[i].endDate = me
            .$moment(arrayLetters[i].endDate, "YYYY-MM-DD")
            .format("YYYY-MM-DD");
        }
        me.letters = arrayLetters;
      });
    },
    listClients() {
      let me = this;
      me.clients = [];
      me.$axios.get(`/client`).then(function(response) {
        let arrayRes = response.data;
        arrayRes.map(p => {
          me.clients.push({
            text: p.comercialName,
            value: p.id
          });
        });
      });
    },
    openURL(url) {
      remote.shell.openExternal(url);
    },
    closeEnd() {
      this.dialogEnd = false;
      this.clean();
    },
    calcTea(tipoDeTasaNominal, capitalizacion, diasDelAnio, tasa) {
      let n = diasDelAnio / capitalizacion;
      let m = tipoDeTasaNominal / capitalizacion;
      return Number(((1 + tasa / 100 / m) ** n - 1) * 100);
    },
    calcData() {
      this.discountedAmount;
    },
    saveEnd() {
      let me = this;
      // this.calcData(item);
      let dias =
        this.$moment(this.dateEnd).diff(
          this.$moment(this.startDateLet),
          "days"
        ) + 1;
      let tasaEfectivaDelPeriodo = (1 + this.tea / 100) ** (dias / 360) - 1;
      console.log(
        "Datediff",
        tasaEfectivaDelPeriodo,
        "date",
        me.startDateLet,
        "end",
        me.dateEnd
      );
      let d = Number(tasaEfectivaDelPeriodo / (1 + tasaEfectivaDelPeriodo));
      let desc = Number(this.nominalValue * d).toFixed(2);
      desc = Number(desc);
      let discountedAmount = this.nominalValue - desc;
      let V = Number(this.nominalValue);
      console.log(
        "this",
        this.costsStartValue,
        this.costsEndValue,
        d,
        discountedAmount,
        desc
      );
      let vr = V + this.costsStartValue;
      let ve = V - desc - this.costsEndValue;
      console.log(vr, ve, 360 / dias, (vr / ve) ** (360 / dias) - 1);
      let tcea = Number((vr / ve) ** (360 / dias) - 1) * 100;
      let endor = {
        tea: me.tea,
        paidDate: me.dateEnd,
        discountedAmount: Number(discountedAmount).toFixed(2),
        recievedAmount:Number(discountedAmount-this.costsStartValue).toFixed(2),
        tcea: tcea,
        buyer: { id: this.buyerId },
        letter: { id: this.letterId },
        costs: me.costs
      };
      console.log("body", JSON.stringify(endor));
      me.$axios.post("/endorsment", endor).then(function(response) {
        // console.log(response);
        if (response.data != true) {
        }

        me.close();
        me.list();
        me.clean();
      });
      this.dialogEnd = false;
    }
  },
  async mounted() {
    if (this.logged) {
      this.list();
      // this.listClients();
    }
    // this.list()

    // console.log(this.$store);
    console.log(
      this.$moment("2019-01-01T23:28:57.000Z", "YYYY-MM-DD").format(
        "YYYY-MM-DD"
      )
    );
  },
  watch: {
    email: function() {},
    dialog(val) {
      val || this.close();
    },
    dates(val) {
      for (let i = 0; i < this.dates.length; i++) {
        const element = this.dates[i];
        if (i + 1 < this.dates.length) {
          if (this.dates[i] > this.dates[i + 1]) {
            this.dates[i] = this.dates[i + 1];
            this.dates[i + 1] = element;
          }
        }
        console.log(this.dates);
      }
    }
  },
  computed: {
    btnDisabled1() {
      const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return !pattern.test(this.email) || this.password.length < 3;
    },
    username() {
      return this.$auth.user.username;
    },
    userID() {
      return this.$auth.user.id;
    },
    logged() {
      return this.$auth.loggedIn;
    },
    formTitle() {
      return this.editedIndex === -1 ? "Nueva Letra" : "Update Letter";
    },
    formTitle2() {
      return this.editedIndex === -1 ? "Vender Letra" : "Update Letter";
    },
    formTitle3() {
      return this.editedIndex === -1 ? "Tasa Nominal" : "Update Letter";
    },
    formTitle4() {
      return this.editedIndex === -1 ? "Agregar Costo" : "Update Letter";
    },
    costsStart() {
      let arr1 = this.costs;
      return arr1.filter(x => {
        return x.start == true;
      });
    },
    costsStartValue() {
      // console.log(this.costsStart)
      let arr1 = this.costsStart;
      let sum = 0;
      for (let i = 0; i < arr1.length; i++) {
        console.log("que", arr1[i].amount);
        sum = sum + Number(arr1[i].amount);
      }
      console.log("suma de cosos1", sum);
      return sum;
    },
    costsEnd() {
      let arr1 = this.costs;
      return arr1.filter(x => {
        return x.start == false;
      });
    },
    costsEndValue() {
      let arr1 = this.costsEnd;
      let sum = 0;

      for (let i = 0; i < arr1.length; i++) {
        console.log("que", arr1[i].amount);
        sum = sum + Number(arr1[i].amount);
      }
      console.log("suma de cosos2", sum);
      return sum;
    }
  }
};
</script>

<style lang="sass">
  .e-nuxt-container 
    min-height: calc(100vh - 50px) 
    background: linear-gradient(to right, #ece9e6, #ffffff) 
    font-family: Helvetica, sans-serif 
    padding: 30px
  .mt-2 
      margin-top: 14px 
  .mb-2 
      margin-bottom: 14px 
  .e-nuxt-content 
      display: flex 
      justify-content: space-around 
      padding-top: 100px 
      align-items: flex-start 
      flex-wrap: wrap 
  .e-nuxt-logo 
      width: 400px 
  .e-nuxt-system-info 
      padding: 20px 
      border-top: 1px solid #397c6d 
      border-bottom: 1px solid #397c6d 
  .e-nuxt-links 
      padding: 100px 0 
      display: flex 
      justify-content: center 
  .e-nuxt-button 
      color: #364758 
      padding: 5px 20px 
      border: 1px solid #397c6d 
      margin: 0 20px 
      border-radius: 15px 
      font-size: 1rem 
      &:hover 
          cursor: pointer 
          color: white 
          background-color: #397c6d 
</style>
