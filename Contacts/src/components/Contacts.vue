<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      :clipped="$vuetify.breakpoint.lgAndUp"
      fixed
      app
    >
      <v-list dense>
        <template v-for="item in items">
          <v-layout
            v-if="item.heading"
            :key="item.heading"
            row
            align-center
          >
            <v-flex xs6>
              <v-subheader v-if="item.heading">
                {{ item.heading }}
              </v-subheader>
            </v-flex>
            <!-- <v-flex
              xs6
              class="text-xs-center"
            >
              <a
                href="#!"
                class="body-2 black--text"
              >РЕДАКТИРОВАТЬ</a>
            </v-flex> -->
          </v-layout>
          <v-list-group
            v-else-if="item.children"
            :key="item.text"
            v-model="item.model"
            :prepend-icon="item.model ? item.icon : item['icon-alt']"
            append-icon=""
          >
            <template v-slot:activator="{ on }">
              <v-list-tile>
                <v-list-tile-content>
                  <v-list-tile-title>
                    {{ item.text }}
                  </v-list-tile-title>
                </v-list-tile-content>
              </v-list-tile>
            </template>
            <!-- <v-list-tile
              v-for="(child, i) in item.children" :key="i" @click=""> -->
            <v-list-tile
              v-for="(child, i) in item.children"
              :key="i"
            >
              <v-list-tile-action v-if="child.icon">
                <v-icon>{{ child.icon }}</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>
                  {{ child.text }}
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-list-group>
          <!-- <v-list-tile v-else :key="item.text" @click=""> -->
          <v-list-tile
            v-else
            :key="item.text"
          >
            <v-list-tile-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>
                {{ item.text }}
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </template>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar
      :clipped-left="$vuetify.breakpoint.lgAndUp"
      color="blue darken-3"
      dark
      app
      fixed
    >
      <v-toolbar-title
        style="width: 300px"
        class="ml-0 pl-3"
      >
        <v-btn    @click="dialog = !dialog"  class="bt" > добавить   </v-btn>

            <v-btn     @click="sortedItems"  class="bun" > sort   </v-btn>





        <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      </v-toolbar-title>
      <v-text-field
        flat
        solo-inverted
        hide-details
        prepend-inner-icon="search"
        label="Поиск"
        class="hidden-sm-and-down"
      ></v-text-field>
      <v-spacer></v-spacer>
    </v-toolbar>
    <v-expansion-panel popout >
      <v-expansion-panel-content v-for="i in users" :key="i.email">
        <template v-slot:header>
          <v-avatar class="text-xs-left"
                  size="40px"
                >
                  <img
                    src="//ssl.gstatic.com/s2/oz/images/sge/grey_silhouette.png"
                    alt=""
                  >
                </v-avatar>
          <div>{{i.name}}</div>
          <div> <v-icon>phone</v-icon> {{i.phone}}</div>
        </template>
        <v-card>
          <v-card-text><v-icon>business</v-icon> Компания: {{i.company}}</v-card-text>
          <v-card-text><v-icon>event</v-icon> Дата рождения: {{i.hireDate}}</v-card-text>
          <v-card-text><v-icon>mail</v-icon> Email: {{i.email}}</v-card-text>
          <v-card-text><v-icon>language</v-icon> Сайт: {{i.site}}</v-card-text>

          <div class="text-xs-center">
            <v-tooltip top>
        <template v-slot:activator="{ on }" >
          <v-btn flat icon color="blue lighten-2" v-on="on" @click="edit"> <v-icon>edit</v-icon> </v-btn>
        </template>
        <span>Изменить </span>
      </v-tooltip>

            <v-tooltip top>
        <template v-slot:activator="{ on }">
          <v-btn flat icon color="red lighten-2" v-on="on" @click="delet(i)"><v-icon>delete</v-icon></v-btn>
        </template>
        <span>Удалить</span>
      </v-tooltip>
        <!-- <v-btn flat icon color="blue lighten-2" :href="source">
          <v-icon>thumb_up</v-icon>
        </v-btn>

        <v-btn flat icon color="red lighten-2">
          <v-icon>thumb_down</v-icon>
        </v-btn> -->
      </div>
        </v-card>
      </v-expansion-panel-content>
    </v-expansion-panel>

    <v-dialog
      v-model="dialog"
      width="600px"
    >
      <v-card>
        <v-card-title class="grey lighten-4 py-4 title">
          Создать контакт
        </v-card-title>
        <v-container
          grid-list-sm
          class="pa-4"
        >
          <v-layout
            row
            wrap
          >
            <v-flex
              xs12
              align-center
              justify-space-between
            >
              <v-layout align-center>
                <v-avatar
                  size="40px"
                  class="mr-3"
                >
                  <img
                    src="//ssl.gstatic.com/s2/oz/images/sge/grey_silhouette.png"
                    alt=""
                  >
                </v-avatar>
                <v-text-field v-model="name" placeholder="ФИО"></v-text-field>
              </v-layout>
            </v-flex>
            <v-flex xs6>
              <v-text-field
                v-model="company"
                prepend-icon="business"
                placeholder="Компания"
              ></v-text-field>
            </v-flex>
            <v-flex xs6>
              <v-flex>
                <v-menu
                  ref="datePicker"
                  :close-on-content-click="false"
                  :nudge-right="40"
                  :return-value.sync="hireDate"
                  lazy
                  transition="scale-transition"
                  offset-y
                  full-width
                  min-width="290px"
                >
                  <v-text-field
                    slot="activator"
                    v-model="hireDate"
                    placeholder="Age"
                    prepend-icon="event"
                    readonly
                  ></v-text-field>
                  <v-date-picker
                    v-model="hireDate"
                    @input="$refs.datePicker.save(hireDate)"
                  ></v-date-picker>
                </v-menu>
              </v-flex>
            </v-flex>
            <v-flex xs6>
              <v-text-field
                v-model="email"
                prepend-icon="mail"
                placeholder="Email"
              ></v-text-field>
            </v-flex>
            <v-flex xs6>
              <v-text-field
                v-model="phone"
                type="tel"
                prepend-icon="phone"
                placeholder="(000) 000 - 0000"
                mask="phone"
              ></v-text-field>
            </v-flex>
            <v-flex xs12>
              <v-text-field
                v-model="site"
                prepend-icon="language"
                placeholder="Site"
              ></v-text-field>
            </v-flex>
          </v-layout>
        </v-container>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            flat
            @click="dialog = false"
          >Отмена</v-btn>
          <v-btn
            flat
            color="primary"
            @click="save"
          >Сохранить</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-app>

</template>

<script>
/*var obj = {
	item1: 1,
	item2: [123, "two", 3.0],
	item3:"hello"
};

var serialObj = JSON.stringify(obj); //сериализуем его

localStorage.setItem("myKey", serialObj); //запишем его в хранилище по ключу "myKey"

var returnObj = JSON.parse(localStorage.getItem("myKey"));*/
////////////////////////////////////////////////////////////////////////////////////


export default {
  data: () => ({
    users:[],
    name:'',
    company:'',
    email:'',
    phone:'',
    site:'',
    dialog: false,
    edit: false,
    drawer: null,
    hireDate: "",
    items: [
      { icon: "contacts", text: "Сортировать по дате рождения" },
    ],
    hireDates: []
  }),


  mounted() {
   if (localStorage.getItem('users')) {
     try {
       this.users = JSON.parse(localStorage.getItem('users'));
     } catch(e) {
       localStorage.removeItem('users');
     }
   }
 },
    methods:{
    save: function () {
      this.users.push({
        name:this.name,
        company:this.company,
        hireDate:this.hireDate,
        email:this.email,
        phone:this.phone,
        site:this.site});
      this.name = '',
      this.company = '',
      this.hireDate = '',
      this.email = '',
      this.phone = '',
      this.site = '';
      this.dialog = false;
       this.saveCats();
    },
    computed: {
      sortedItems: function() {
        return this.users.sort((a, b) => new Date(a.hireDate) - new Date(b.hireDate))
      }
    },
    delet:function(i){
      this.users.splice(i,1);
       this.saveCats();
    },
    saveCats() {
          const parsed = JSON.stringify(this.users);
          localStorage.setItem('users', parsed);
        }
  },
};
</script>


<style scoped>
h1 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.bt {
  width: 70px;
  color: "yellow";
  padding: 5  ;

  height: 30px;
}
.bun {
  width: 50px;
  color: 	#7CFC00;
  padding: 5  ;

  height: 30px;
}

</style>
