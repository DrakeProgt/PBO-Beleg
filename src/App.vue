<template>
  <div>
    <!-- Navbar -->
    <b-navbar variant="dark" type="dark">
      <b-container>
        <b-navbar-brand><img src="Dateien/logo.png" class="d-inline-block align-top"> PBO-Beleg</b-navbar-brand>
      </b-container>
    </b-navbar>

    <b-container>
      <b-btn class="btn-block mt-5" :variant=p v-b-toggle.collapse1 @click.stop="details=!details; p=p=='primary'?'success':'primary'">Steakholder {{ !details ? 'Zuklappen' : 'Ausklappen' }}</b-btn>
      <b-collapse id="collapse1" class="mt-3">
        <b-card>
          <ul class="list-group">
            <li class="list-group-item"></li>
          </ul>
        </b-card>
      </b-collapse>
    </b-container>
  </div>
</template>

<script>
import json from './Dateien/process.json';
  export default {
    data () {
      return {
        details: true,
        p: "success",
        json: json
      }
    },

    computed: {

    },

    methods: {

    }
  }
</script>

<style>
    
</style>