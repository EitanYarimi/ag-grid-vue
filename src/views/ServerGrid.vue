<template>
  <div id="app" style="height: 100%">
    <ag-grid-vue
      style="width: 1000px; height: 500px;"
      class="ag-theme-alpine"
      id="myGrid"
      :gridOptions="gridOptions"
      @grid-ready="onGridReady"
      :columnDefs="columnDefs"
      :defaultColDef="defaultColDef"
      :animateRows="true"
      :pagination="true"
      :rowModelType="rowModelType"
      :serverSideStoreType="serverSideStoreType"
      :frameworkComponents="frameworkComponents"
      @filter-changed="onFilterChanged"
    >
    </ag-grid-vue>
  </div>
</template>

<script>
import { AgGridVue } from "ag-grid-vue";
import FakeServer from "../FakeServer";
import nameFilter from "../components/nameFilter"

export default {
  name: "ServerGrid",
  components: {
    AgGridVue,
  },
  data() {
    return {
      gridOptions: null,
      gridApi: null,
      columnApi: null,
      columnDefs: null,
      defaultColDef: null,
      rowModelType: null,
      serverSideStoreType: null,
      selectedCountries: null,
      fakeServer: null,
      frameworkComponents: null,
    };
  },
  beforeMount() {
    this.gridOptions = {};
    this.columnDefs = [
      {
        field: "country",
        
        // filter: 'agSetColumnFilter',
        // filterParams:{values: this.getCountryValuesAsync},
        filter: 'agMultiColumnFilter',
        filterParams: { filters: [
          {filter: 'nameFilter',},
            {
                filter: 'agTextColumnFilter',
                
            },
            {
                filter: 'agSetColumnFilter',
                filterParams:{values: this.getCountryValuesAsync}
            }
        ] },
        menuTabs: ["filterMenuTab"],
         
      },
      {
        field: "sport",
        filter: "agSetColumnFilter",
        filterParams: { values: this.getSportValuesAsync },
        menuTabs: ["filterMenuTab"],
      },
      {
        field: "athlete",
        filter: 'nameFilter',
        menuTabs: ["filterMenuTab"],
      },
    ];
    this.defaultColDef = {
      flex: 1,
      minWidth: 150,
      sortable: true,
      resizable: true,
    };
    this.rowModelType = "serverSide";
    this.serverSideStoreType = "partial";
    this.cacheBlockSize = 100;
    this.maxBlocksInCache = 10;
     this.frameworkComponents = { nameFilter: nameFilter };
  },
  mounted() {
    this.gridApi = this.gridOptions.api;
    this.gridColumnApi = this.gridOptions.columnApi;
  },
  methods: {
    getCountryValuesAsync(params) {
      var countries = this.fakeServer.getCountries();
      setTimeout(function() {
        params.success(countries);
      }, 500);
    },
    getSportValuesAsync(params) {
      var sports = this.fakeServer.getSports(this.selectedCountries);
      setTimeout(function() {
        params.success(sports);
      }, 500);
    },
    areEqual(a, b) {
      if (a == null && b == null) {
        return true;
      }
      if (a != null || b != null) {
        return false;
      }
      return (
        a.length === b.length &&
        a.every(function(v, i) {
          return b[i] === v;
        })
      );
    },
    onFilterChanged() {
      var countryFilterModel = this.gridApi.getFilterModel()["country"];
      var selected = countryFilterModel && countryFilterModel.values;
      if (!this.areEqual(this.selectedCountries, selected)) {
        this.selectedCountries = selected;
        console.log("Refreshing sports filter");
        var sportFilter = this.gridApi.getFilterInstance("sport");
        sportFilter.refreshFilterValues();
      }
    },
    ServerSideDatasource(server) {
      return {
        getRows: function(params) {
          console.log(
            "[Datasource] - rows requested by grid: ",
            params.request
          );
          var response = server.getData(params.request);
          setTimeout(function() {
            if (response.success) {
              params.success({
                rowData: response.rows,
                rowCount: response.lastRow,
              });
            } else {
              params.fail();
            }
          }, 500);
        },
      };
    },
    onGridReady(params) {
      const httpRequest = new XMLHttpRequest();
      const updateData = (data) => {
        this.fakeServer = new FakeServer(data);
        
        var datasource = new this.ServerSideDatasource(this.fakeServer);
        params.api.setServerSideDatasource(datasource);
      };

      httpRequest.open(
        "GET",
        "https://www.ag-grid.com/example-assets/olympic-winners.json"
      );
      httpRequest.send();
      httpRequest.onreadystatechange = () => {
        if (httpRequest.readyState === 4 && httpRequest.status === 200) {
          updateData(JSON.parse(httpRequest.responseText));
        }
      };
    },
  },
};
</script>
<style lang="scss">
@import "../../node_modules/ag-grid-community/dist/styles/ag-grid.css";
@import "../../node_modules/ag-grid-community/dist/styles/ag-theme-alpine.css";
</style>
