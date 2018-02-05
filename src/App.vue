<template>
  <div>
    <!-- Navbar -->
    <b-navbar variant="dark" type="dark">
      <b-container>
        <b-navbar-brand><img src="Dateien/logo.png" class="d-inline-block align-top"> PBO-Beleg</b-navbar-brand>
      </b-container>
    </b-navbar>

    <canvas id="myChart"></canvas>

    <b-container>
      <b-table striped hover :items="stakeholder" :fields="fields" outlined>
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
      }
    },
    beforeMount(){
      this.fillArray();
    },
    mounted() {      
        var myChart = "myChart";
        var ctx = document.getElementById(myChart);
        var myChart = new Chart(ctx, {
          responsive: true,
          type: 'line',
          data: {
            labels: ["Red", "Blue", "Yellow", "Green", "Purple", "Orange"],
            datasets: [{
              label: 'Anzahl',
              data: [12, 19, 3, 5, 2, 3]           
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
                HighlightFill: 'rgba(25, 15, 51, 1)',
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
            layout: {
              padding: 20
            },
            scales: {
              yAxes: [{
                scaleLabel: {
                  fontColor: '#123'
                },
                ticks: {
                  beginAtZero: true,
                  stepSize: 1,
                  max: 20
                }
              }],
              xAxes: [{
                scaleLabel: {
                  fontColor: "green"
                }
              }]
            }
          }
        });
    },
    methods: {
      fillArray: function(){
        var vm=this;        
        JSON.process.stakeholder.forEach(function(child){
          vm.stakeholder.push({name: child.name, projekt: vm.countInit(child.id), namen: vm.collectNames(child.id), finish: vm.countEnd(child.id), part: vm.part(child.id)});
        });
      },
      chart: function(zahl){
        var myChart = "myChart"+String(zahl);
        var ctx = document.getElementById(myChart);
        var myChart = new Chart(ctx, {
          responsive: true,
          type: 'line',
          data: {
            labels: ["Red", "Blue", "Yellow", "Green", "Purple", "Orange"],
            datasets: [{
              label: '# of Votes',
              data: [12, 19, 3, 5, 2, 3],
              backgroundColor: [
                'rgba(255, 99, 132, 0.2)',
                'rgba(54, 162, 235, 0.2)',
                'rgba(255, 206, 86, 0.2)',
                'rgba(75, 192, 192, 0.2)',
                'rgba(153, 102, 255, 0.2)',
                'rgba(255, 159, 64, 0.2)'
              ],
              borderColor: [
                'rgba(255,99,132,1)',
                'rgba(54, 162, 235, 1)',
                'rgba(255, 206, 86, 1)',
                'rgba(75, 192, 192, 1)',
                'rgba(153, 102, 255, 1)',
                'rgba(255, 159, 64, 1)'
              ],
              borderWidth: 1
            }]
          },
          options: {
            scales: {
              yAxes: [{
                ticks: {
                  beginAtZero:true,
                  stepSize: 1
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
      }
    }
  }
</script>

<style>
  .chart-container {  
  margin: auto;
  width: 99%;
  }
</style>