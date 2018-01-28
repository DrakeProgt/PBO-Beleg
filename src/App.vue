<template>
  <div>
    <!-- Navbar -->
    <b-navbar variant="dark" type="dark">
      <b-container>
        <b-navbar-brand><img src="Dateien/logo.png" class="d-inline-block align-top"> PBO-Beleg</b-navbar-brand>
      </b-container>
    </b-navbar>

    <b-container>
      <b-btn class="btn-block mt-5" :variant='p' v-b-toggle.collapse1 @click.stop="details=!details; p=p=='primary'?'success':'primary'">Stakeholder {{ !details ? 'Zuklappen' : 'Ausklappen' }}</b-btn>
      <b-collapse id="collapse1" class="mt-3">
        <b-card no-body border-variant="dark" header-bg-variant="dark" header-text-variant="white">
          <b-form-checkbox slot="header" v-model="anzeigen">Einfärben</b-form-checkbox>
          <b-list-group flush>
            <b-list-group-item v-for="item in json.process.stakeholder" :key="item" :variant='anzeigen ? caseInit(countInit(item.id)) : ""'>
              <h4 class="list-group-item-heading">{{item.name}}</h4>
              <p class="list-group-item-text projekte">Hat {{countInit(item.id)}} Projekte</p>
            </b-list-group-item>
          </b-list-group>
        </b-card>
      </b-collapse>
    </b-container>
  </div>
</template>

<script>
import JSON from '../Dateien/process.json';
  export default {
    data () {
      return {
        details: true,
        p: "success",
        anzeigen: false,
        name: null,
        projekte: null,
        json: JSON,
      }
    },
    methods: {
      countInit: function(name){
        var count=0;
        JSON.process.childs.forEach(function(child){
          if(child.initiator==name){
            count++;
          }
        });
        return count;
      },
      caseInit(zahl){
        switch(zahl){
          case 0:
            return "primary";
            break;
          case 1: case 2: case 3: case 4: case 5:
            return "success";
            break;
          case 6: case 7: case 8: case 9: case 10:
            return "warning";
            break;
          default:
            return "danger";  
        }
      }
    }
  }
</script>

<style>
    
</style>