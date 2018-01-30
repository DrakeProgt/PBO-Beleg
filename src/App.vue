<template>
  <div>
    <!-- Navbar -->
    <b-navbar variant="dark" type="dark">
      <b-container>
        <b-navbar-brand><img src="Dateien/logo.png" class="d-inline-block align-top"> PBO-Beleg</b-navbar-brand>
      </b-container>
    </b-navbar>
    <canvas id="myChart" style="width:100px; height:100px"></canvas>
    <b-container>
      <b-table striped hover :items="stakeholder" :fields="fields" outlined></b-table>
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
          label: 'Anzahl der initiierten Projekte',
          sortable: true
        },
        {
          key: 'details',
          label: 'Zeige Details'
        }
      ],
        stakeholder:[],
      }
    },
    beforeMount(){
      this.fillArray();
    },
      mounted() {
          var ctx = document.getElementById("myChart");
          var myChart = new Chart(ctx, {
          type: 'bar',
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
                        beginAtZero:true
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
          vm.stakeholder.push({name: child.name, projekt: vm.countInit(child.id)});
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
      }
    }
  }
</script>

<style>
    
</style>