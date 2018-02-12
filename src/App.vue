<template>
  <div>

    <b-navbar type="dark">
      <b-container>
        <b-navbar-brand>Stakeholder</b-navbar-brand>
      </b-container>
    </b-navbar>

    <b-container>
      <b-row>
      <b-col md="6" class="my-4">
        <b-form-group horizontal label="Filter:" class="mb-0">
          <b-input-group>
            <b-form-input v-model="filter" placeholder="Suchbegriff" />
            <b-input-group-button>
              <b-btn :disabled="!filter" @click="filter = ''">Säubern</b-btn>
            </b-input-group-button>
          </b-input-group>
        </b-form-group>
      </b-col>
      <b-col md="6" class="my-4">
        <b-form-group horizontal label="Sortierung:" class="mb-0">
          <b-input-group>
            <b-form-select v-model="sortBy" :options="sortOptions">
              <option slot="first" :value="null">keine</option>
            </b-form-select>
            <b-input-group-button>
              <b-form-select :disabled="!sortBy" v-model="sortDesc">
                <option :value="false">Asc</option>
                <option :value="true">Desc</option>
              </b-form-select>
            </b-input-group-button>
          </b-input-group>
        </b-form-group>
      </b-col>
      <b-col md="6" class="my-2">
        <b-pagination :total-rows="totalRows" :per-page="perPage" v-model="currentPage" class="my-0" />
      </b-col>
      <b-col md="6" class="my-2">
        <b-form-group horizontal label="pro Seite:" class="mb-4">
          <b-form-select :options="pageOptions" v-model="perPage" />
        </b-form-group>
      </b-col>
    </b-row>

    <b-table 
      striped 
      hover 
      fixed
      :items="stakeholder" 
      :fields="fields" 
      outlined 
      show-empty 
      :current-page="currentPage"
      :per-page="perPage"
      :filter="filter"
      :sort-by.sync="sortBy"
      :sort-desc.sync="sortDesc"
      @filtered="onFiltered">

        <template slot="details" slot-scope="row">      
          <b-button size="sm" @click.stop="row.toggleDetails">
            {{ row.detailsShowing ? 'Weniger' : 'Mehr'}} Details
          </b-button>      
        </template>
        <template slot="row-details" slot-scope="row">
         <b-card>
            <b-row class="mb-4">
              <b-col sm="4" class="text-sm-right"><b>Name der initiierten Events:</b></b-col>
              <b-col>{{ row.item.namen }}<br><u>Davon abgeschlossen:</u> {{ row.item.finish }}</b-col>
            </b-row>
            <b-row class="mb-4">
              <b-col sm="4" class="text-sm-right"><b>Zusätzlich beteiligt an folgenden Events:</b></b-col>
              <b-col>{{ row.item.part }}</b-col>
           </b-row>
            <b-container class="chart-container">
            <b-button size="sm" v-b-toggle="'collapse'+String(row.index)" variant="success" v-on:click="chart(row.index)">Diagramm</b-button>
           <b-collapse :id='"collapse"+String(row.index)' class="mt-2">
             <b-card>            
                <canvas :id='"myChart"+String(row.index)'></canvas>              
              </b-card>
            </b-collapse>
            </b-container>
          </b-card>
        </template>
      </b-table>
    </b-container>

  </div>
</template>

<script>
import JSON from '../Dateien/process.json';

  export default {
  name: "app",
    data () {
      return {
        json: JSON,
        fields: [
          {
            key: 'name',
            label: 'Name',
            sortable: true
          },
          {
            key: 'projekt',
            label: 'Anzahl der initiierten Events',
            sortable: true
          },
          {
            key: 'details',
            label: 'Details'
          }
        ],
        stakeholder:[],
        years:[],
        currentPage: 1,
        perPage: 5,
        totalRows: 0,
        pageOptions: [ 5, 10, 15, 20 ],
        sortBy: null,
        sortDesc: false,
        filter: null
      }
    },
    beforeMount(){
      this.fillArray();
      this.totalRows=this.stakeholder.length;
    },
    mounted() {      
        
    },
    computed: {
      sortOptions () {
        return this.fields
          .filter(f => f.sortable)
          .map(f => { return { text: f.label, value: f.key } })
       }
    },
    methods: {
      fillArray: function(){
        var vm=this;        
        JSON.process.stakeholder.forEach(function(child){
          vm.stakeholder.push({name: child.name, projekt: vm.countInit(child.id), namen: vm.collectNames(child.id), finish: vm.countEnd(child.id), part: vm.part(child.id)});
        });
        JSON.process.childs.forEach(function(child){
          if(!vm.years.includes(child.start.substr(0,4))){
            vm.years.push(child.start.substr(0,4));
          }
        });
        vm.years.sort();        
      },
      chart: function(zahl){
        var yearsCounter=[];
        var vm = this;
        var count=0;
        vm.years.forEach(function(year){
          count=0
          JSON.process.childs.forEach(function(child){
            if(year==child.start.substr(0,4) && child.initiator==JSON.process.stakeholder[zahl].id){
              count++;
            }
          });
          yearsCounter.push(count);
        });
        var myChart = "myChart"+String(zahl);
        var ctx = document.getElementById(myChart);
        var myChart = new Chart(ctx, {
          responsive: true,
          type: 'line',
          data: {
            labels: this.years,
            datasets: [{
              label: 'Anzahl',
              data: yearsCounter    
            }]
          },
          options: {
            tooltips: {
              displayColors: false,
              titleFontSize: 14,
              titleMarginBottom: 10,
              titleFontStyle: 'bold',
              xPadding: 20,
              yPadding: 10
            },
            elements: {
              point: {
                hitRadius: 20,
                pointStyle: 'rect',
                backgroundColor: 'rgba(255, 153, 51, 1)',
                borderColor: 'rgba(255, 153, 51, 1)',
                radius: 5,
                hoverRadius: 8
              },
              line: {
                tension: 0,
                backgroundColor: 'rgba(255, 153, 51, 0.2)',
                borderColor: 'rgba(255, 153, 51, 1)',
                borderWidth: 2
              }
            },
            legend: {
              display: false
            },
            scales: {
              yAxes: [{
                scaleLabel: {
                  display: true,
                  labelString: 'Anzahl der initiierten Events',
                  fontColor: '#000',
                  fontStyle: 'bold',
                  fontSize: 16
                },
                ticks: {
                  beginAtZero: true,
                  stepSize: 1,
                  max: Math.max.apply(null, yearsCounter)+1
                }
              }],
              xAxes: [{
                scaleLabel: {
                  display: true,
                  labelString: 'Jahre',
                  fontColor: '#000',
                  fontStyle: 'bold',
                  fontSize: 16
                }
              }]
            }
          }
        });
      },
      countInit: function(name){
        var count=0;
        JSON.process.childs.forEach(function(child){
          if(child.initiator==name){
            count++;
          }
        });
        return count;
      },
      collectNames: function(name){
        var namen="";
        JSON.process.childs.forEach(function(child){
          if(child.initiator==name){
            if(namen==""){
              namen=namen.concat(child.name);              
            }else{
              namen=namen.concat(" | "+child.name)              
            }            
          }
        });
        if(namen==""){
          return "-"
        }

        return namen;
      },
      countEnd: function(name){
        var count=0;
        JSON.process.childs.forEach(function(child){
          if(child.initiator==name){
            if(child['end (optional)']!=""){
                count++;
            }           
          }
        });
        
        return count;
      },
      part: function(name){
        var part="";
        JSON.process.childs.forEach(function(child){
          child.participants.forEach(function(partici){
            if(partici==name){
              if(part==""){
                part=part.concat(child.name);              
              }else{
                part=part.concat(" | "+child.name)              
              }
            }
          });
        });
        if(part==""){
          return "-"
        }

        return part;
      },
      onFiltered (filteredItems) {      
        this.totalRows = filteredItems.length;
        this.currentPage = 1;
      },
      lengthStake: function(){
        return stakeholder.length();
      }
    }
  }
</script>

<style>
  .chart-container {  
    margin: auto;
    width: 99%;
  }

  .table-hover tbody tr:hover td {
    background-color: #4CA2FF;
  }

  .navbar {
    background: #00388C;
  }

  .navbar-brand {
    font-size: xx-large;
    padding: 10px 0px 10px 0px;
  }
</style>