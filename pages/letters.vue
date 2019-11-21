<template>
  <div class="e-nuxt-container">
    <h2>Bienvenidos</h2>

      <v-layout align-start>
        <v-flex>
          <v-toolbar flat color="white">
            <v-toolbar-title>Letters</v-toolbar-title>
            <v-divider class="mx-2" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-text-field
              class="text-xs-center"
              v-model="search"
              append-icon="mdi-anchor"
              label="Search"
              single-line
              hide-details
              color="green"
            ></v-text-field>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <template v-slot:activator="{ on }">
                <v-btn v-on="on" color="green" dark class="mb-2">New</v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="headline">{{ formTitle }}</span>
                </v-card-title>
                <v-card-text>
                  <v-container grid-list-md>
                    <v-layout wrap>
                      <v-flex xs12 sm12 md12>
                        <v-text-field v-model="correlative" label="correlativo" type="number"></v-text-field>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                           <v-date-picker v-model="dates" range></v-date-picker>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-text-field v-model="nominalValue" label="Valor Nominal"></v-text-field>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-select v-model="moneyType" :items="moneyTypes" label="Tipo de moneda"></v-select>
                      </v-flex>
                      <v-flex xs12 sm12 md12>
                        <v-select v-model="clientId" :items="clients" label="Cliente"></v-select>
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="green" text @click.native="close">Cancel</v-btn>
                  <v-btn color="green" text @click.native="save">Save</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
          <v-data-table :headers="headers" :items="letters" :search="search" class="elevation-1">
            <template v-slot:item.usable="{ item }">
              <!-- <v-icon>{{ item.usable ? "mdi-checkbox-marked" : "mdi-checkbox-blank-outline" }}</v-icon> -->
            </template>
            <template v-slot:item.action="{ item }">
               <v-btn color="white"  class="mb-2" @click="newEndorsement(item)" >New Endrosement</v-btn>
              <!-- <v-icon small class="mr-2" @click="executeItem(item.command)">exec</v-icon>
              <v-icon small class="mr-2" @click="editItem(item.command)">edit</v-icon>
              <v-icon small @click="deleteItem(item.command)">delete</v-icon> -->
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
      externalContent: "",
      audio_stream: null,
      audio_context: null,
      recorder: null,
      loginLoading: false,
      email: "",
      password: "",
      letters: [],
      dialog: false,
      headers: [
        { text: "Correlativo", value: "correlative", sortable: false },
        { text: "Fecha de inicio", value: "startDate", sortable: false },
        { text: "Fecha de vencimiento", value: "endDate", sortable: false }, ///CAMPOS
        { text: "Valor nominal", value: "nominalValue", sortable: false }, ///CAMPOS
        { text: "Actions", value: "action", sortable: false }
      ],
      search: "",
      editedInde: -1,
      id: "",
      userId: "",
      clientId: "",
      correlative: "",
      startDate: "",
      endDate: "",
      nominalValue: "",
      moneyTypes:[
        'Nuevo Sol', 'Dolar americano'
      ],
      moneyType: "",
      endorsmentId: "",
      editedIndex: -1,
      dates:[],
      clients:[]
    };
  },
  created() {},
  methods: {
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
        me.$axios
          .post("/letter", {
            correlative: me.correlative,
            user: { id: this.userID },
            client: { id: this.clientId },
            startDate: this.dates[0],
            endDate: this.dates[1],
            nominalValue: Number(me.nominalValue),
            moneyType: "sol"
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
      this.clientId= "",
      this.correlative= "",
      this.startDate= "",
      this.endDate= "",
      this.nominalValue= "",
      this.moneyTypes=[
        'Nuevo Sol', 'Dolar americano'
      ],
      this.moneyType= ""
    },
    newEndorsement(item){
      console.log(item)
    },
    list() {
      let me = this;
      me.$axios.get(`/letter/${this.userID}`).then(function(response) {
        me.letters = response.data;
      });
    },
    listClients(){
           let me = this;
          me.clients=[]
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
    }
  },
  async mounted() {
    if (this.logged) {
      this.list();
      this.listClients()
    }
    // this.list()
    console.log(this.$store);
  },
  watch: {
    email: function() {},
    dialog(val) {
      val || this.close();
    },
    dates(val){
      for (let i = 0; i < this.dates.length; i++) {
        const element = this.dates[i];
        if(i+1<this.dates.length){
          if(this.dates[i]>this.dates[i+1]){
            this.dates[i]=this.dates[i+1]
            this.dates[i+1]=element
          }
        }
        console.log(this.dates)
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
      return this.editedIndex === -1 ? "New Command" : "Update Command";
    }
    // dates(){
    //   console.log(dates)
    //   return dates
    // }
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
