<template>
  <f7-page name="home" @page:init="onPageInit">
    <f7-navbar :sliding="false" large>
      <div class="right">


        <div class="searchbar searchbar-inline">
          <div class="searchbar-input-wrap">
            <input type="text" placeholder="Search..." id="autocomplete-dropdown">
            <i class="searchbar-icon"></i>
            <div class="input-clear-button"></div>
          </div>
        </div>


      </div>
      <f7-nav-title-large sliding>Corona Virus Tracker</f7-nav-title-large>
    </f7-navbar>

    <f7-block-title>Latest Updates</f7-block-title>
    <f7-block strong>
      <f7-row>
        <f7-col>
          <f7-card title="Infected">
            <f7-card-content :padding="false">
              <f7-list medial-list>
                <f7-list-item>
                  <div class="odometer" style="font-size: 40px;" id="confirmed"> {{confirmed}} </div>
                </f7-list-item>
              </f7-list>
            </f7-card-content>
             <f7-card-footer>
              <span>Last Update: {{lastUpdate}}</span>
            </f7-card-footer>
          </f7-card>
        </f7-col>
        <f7-col>
          <f7-card title="Recovered">
            <f7-card-content :padding="false">
              <f7-list medial-list>
                <f7-list-item>
                  <div class="odometer" style="font-size: 40px;" id="recovered"> {{recovered}} </div>
                </f7-list-item>
              </f7-list>
            </f7-card-content>
             <f7-card-footer>
              <span>Last Update: {{lastUpdate}}</span>
            </f7-card-footer>
          </f7-card>
        </f7-col>
        <f7-col>
          <f7-card title="Deaths">
            <f7-card-content :padding="false">
              <f7-list medial-list>
                <f7-list-item>
                  <div class="odometer" style="font-size: 40px;" id="deaths"> {{deaths}} </div>
                </f7-list-item>
              </f7-list>
            </f7-card-content>
             <f7-card-footer>
              <span>Last Update: {{lastUpdate}}</span>
            </f7-card-footer>
          </f7-card>
        </f7-col>
      </f7-row>
    </f7-block>

    <f7-block-title>Map</f7-block-title>
    <f7-block strong>
      <f7-card class="demo-facebook-card">
        <f7-card-content :padding="false">
          <div id="vmap" :style="{height: '300px'}">
          </div>
        </f7-card-content>
      </f7-card>
    </f7-block>

    <f7-block-title>Statistics Chart</f7-block-title>
    <f7-block strong>
      <f7-row>
        <f7-col>
          <f7-card>
            <div v-if="(confirmedData !== null)">
              <DailyReport :labels="labels" :confirmedData="confirmedData" :deathsData="deathsData"></DailyReport>
            </div>
          </f7-card>
        </f7-col>
        <f7-col>
          <f7-card>
            <div v-if="(confirmed !== null)">
              <CountryChart></CountryChart>
            </div>
          </f7-card>
        </f7-col>
      </f7-row>
    </f7-block>

    <div class="block-footer" >
      <div>Developed by Ajith Kp (ajithkp560) ~ <a href="http://terminalcoders.blogspot.com">Blog</a></div>
    </div>
  </f7-page>
  
</template>

<script>
import axios from 'axios';
import DailyReport from './DailyReport';
import CountryChart from './CountryChart';
import { f7, f7ready } from 'framework7-vue';
import Vuex from 'vuex';

export default {
  data () {
    return {
      confirmed: null,
      deaths: 0,
      recovered: 0,
      lastUpdate: '',
      countries: [],
      data: [],
      labels: null,
      confirmedData: null,
      deathsData: null,
      recoveredData: null,
      country: 'Global',
    }
  },
  methods: {
      loadGlobalData(){
        const vm = this;
        axios.get('https://covid19.mathdro.id/api')
        .then(response => {
          const { data: { confirmed, recovered, deaths, lastUpdate } } = response;
          this.confirmed = confirmed.value;
          this.deaths = deaths.value;
          this.recovered = recovered.value;
          this.lastUpdate = new Date(lastUpdate).toDateString();
          $('#confirmed').html(this.confirmed);
          $('#deaths').html(this.deaths);
          $('#recovered').html(this.recovered);
          vm.$store.state.CountryChartData = [this.confirmed, this.deaths, this.recovered];
          vm.$store.state.countryData = 'Global';
          //var odometer = new Odometer({ el: $('.odometer')[0], value: this.confirmed, theme: 'train-station' });
        });
      },
      loadDailyData(){
        axios.get('https://covid19.mathdro.id/api/daily')
        .then(response => {
          const info = response.data.map(({confirmed, recovered, deaths, reportDate}) => ({ confirmed: confirmed.total, deaths: deaths.total, recovered: recovered.total, reportDate: reportDate }));
          this.confirmedData = info.map((data) => data.confirmed).slice(info.length-30, info.length);
          this.deathsData = info.map((data) => data.deaths).slice(info.length-30, info.length);;
          this.labels = info.map((data) => data.reportDate).slice(info.length-30, info.length);;
        });
      },
      loadCountryData(value){
        const vm = this;
        axios.get('https://covid19.mathdro.id/api/countries/'+value)
        .then(response => {
          const { data: { confirmed, recovered, deaths, lastUpdate } } = response;
          this.confirmed = confirmed.value;
          this.deaths = deaths.value;
          this.recovered = recovered.value;
          this.lastUpdate = new Date(lastUpdate).toDateString();
          $('#confirmed').html(this.confirmed);
          $('#deaths').html(this.deaths);
          $('#recovered').html(this.recovered);
          vm.$store.state.CountryChartData = [this.confirmed, this.deaths, this.recovered];
          vm.$store.state.countryData = this.country;
          //this.$forceUpdate();
        });
      },

      onPageInit(e, page) {
        const vm = this;

        this.loadGlobalData();
        this.loadDailyData();


        var autocompleteDropdownSimple = this.$f7.autocomplete.create({
          inputEl: '#autocomplete-dropdown',
          openIn: 'dropdown',
          preloader: true,
          limit: 10,
          expandInput: true,
          source: function (query, render) {
            const autocomplete = this;
            const results = [];
            if (query.length === 0) {
              render(results);
              return;
            }
            autocomplete.preloaderShow();
            axios.get('https://covid19.mathdro.id/api/countries')
            .then(response => {
              const { data: { countries } } = response;
              countries.push({name: 'Global'});
              for(let i=0;i<countries.length;i++){
                if (countries[i].name.toLowerCase().indexOf(query.toLowerCase()) === 0) results.push(countries[i].name);
              }
              autocomplete.preloaderHide();
              render(results);
            });
          },
          on: {
            change: function(value){
              if(value!='Global'){
                vm.country = value;
                vm.loadCountryData(value);
              } else{
                vm.loadGlobalData();
              }
            }
          }
        });
        
        
        jQuery('#vmap').vectorMap({
            map: 'world_en',
            backgroundColor: '#fff',
            color: '#ffffff',
            hoverOpacity: 0.7,
            selectedColor: '#666666',
            enableZoom: false,
            showTooltip: true,
            values: sample_data,
            scaleColors: ['#C8EEFF', '#006491'],
            normalizeFunction: 'polynomial',
            onRegionClick(element, code, region){
              this.country = region;
            },
            onRegionSelect(element, code, region){
              vm.loadCountryData(code.toUpperCase());
            },
            onRegionDeselect(element, code, region){
              vm.loadGlobalData();
            }
        });
      }
  },
  components: {
    DailyReport,
    CountryChart,
  }
}
</script>

<style>
@import '../static/css/jqvmap.min.css';
@import '../static/css/odometer-theme-train.css';

.jqvmap-label {
  z-index:999999;
}

</style>