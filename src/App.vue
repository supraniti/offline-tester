<template>
  <v-app>
    <v-app-bar
      app
      color="black"
      dark
    >
      תצוגת מבחן מפורטת עבור - {{firstName + ' ' + lastName }}
      <v-spacer></v-spacer>
    </v-app-bar>
    <v-content>
      <v-card
        v-for="(test,index) in tests"
        :key="index"
        outlined
      >
        <v-card-title>מבחן מספר - {{index + 1}} ({{test.result}})</v-card-title>
        <v-card-text>
          <v-list>
            <v-list-group
              v-for="(question,index) in test.questions"
              :key="index"
              v-model="active[index]"
              :prepend-icon="getResult(test,question,index)"
              no-action
            >
              <template v-slot:activator>
                <v-list-item-content>
                  <v-list-item-title v-html="questions[question].question"></v-list-item-title>
                </v-list-item-content>
              </template>

              <v-list-item
                v-for="(answer,index_) in questions[question].answers"
                :key="index_"
              >
                <v-list-item-icon>
                  <v-icon v-if="isChecked(test,question,index,answer,index_)">mdi-check-all</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title
                    v-text="answer.content"
                    :class="answer.correct ? 'green--text' : 'red--text'"
                  ></v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-group>
          </v-list>
        </v-card-text>
      </v-card>
    </v-content>
  </v-app>
</template>
<script>
const qs = (function(a) {
    if (a == "") return {};
    var b = {};
    for (var i = 0; i < a.length; ++i)
    {
        var p=a[i].split('=', 2);
        if (p.length == 1)
            b[p[0]] = "";
        else
            b[p[0]] = decodeURIComponent(p[1].replace(/\+/g, " "));
    }
    return b;
})(window.location.search.substr(1).split('&'));
export default {
  name: 'App',
  components: {},
  data: () => ({
    firstName:null,
    lastName:null,
    tests:null,
    questions:null,
    active:[]
    //
  }),
  mounted(){
    fetch('http://gelms.s537.upress.link/wp-admin/admin-ajax.php?action=singlequizequery&pid=' + qs['pid'])
    .then(response=>{
      return response.json();
    })
    .then(json=>{
      this.firstName = json.firstName;
      this.lastName = json.lastName;
      this.tests = json.tests;
      this.questions = json.questions;
      console.log(json);
    })
  },
  methods:{
    getResult(test,question,index){
      if (test.answers[index] < 0 ){
        return 'mdi-alert-rhombus-outline'
      }else{
        if (this.questions[question].answers[test.answers[index]].correct){
          return 'mdi-check-bold'
        }else{
          return 'mdi-close'
        }
      }
    },
    isChecked(test,question,index,answer,index_){
      return index_ == test.answers[index];
    }
  }
};
</script>
