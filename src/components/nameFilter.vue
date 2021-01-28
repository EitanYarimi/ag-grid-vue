<template>
  <div>
    <div class="ag-set-filter">
      <div ref="eFilterLoading" class="ag-filter-loading ag-hidden">
        Loading...
      </div>
      <!--AG-INPUT-TEXT-FIELD-->
      <div
        role="presentation"
        class="ag-mini-filter ag-labeled ag-label-align-left ag-text-field ag-input-field"
        ref="eMiniFilter"
      >
        <div
          ref="eLabel"
          class="ag-input-field-label ag-label ag-hidden ag-text-field-label"
        ></div>
        <div
          ref="eWrapper"
          class="ag-wrapper ag-input-wrapper ag-text-field-input-wrapper"
          role="presentation"
        >
          <input
            ref="eInput"
            class="ag-input-field-input ag-text-field-input"
            type="text"
           v-model="text"
            aria-label="Search filter values"
            placeholder="Search..."
          />
        </div>
      </div>
      <div ref="eFilterNoMatches" class="ag-filter-no-matches ag-hidden">
        No matches.
      </div>
      <div ref="eSetFilterList" class="ag-set-filter-list" role="presentation">
        <div
          class="ag-virtual-list-viewport ag-filter-virtual-list-viewport ag-focus-managed"
          role="listbox"
        >
          <div
            class="ag-tab-guard ag-tab-guard-top"
            role="presentation"
            tabindex="0"
          ></div>
          <div
            class="ag-virtual-list-container ag-filter-virtual-list-container"
            ref="eContainer"
            style="height: 2664px;"
          >
            <div
              class="ag-virtual-list-item ag-filter-virtual-list-item"
              role="option"
              aria-setsize="111"
              aria-posinset="1"
              tabindex="-1"
              aria-selected="true"
              aria-checked="true"
              style="height: 24px; top: 0px;"
            >
              <div class="ag-set-filter-item">
                <!--AG-CHECKBOX-->
                <div
                  role="presentation"
                  ref="eCheckbox"
                  class="ag-set-filter-item-checkbox ag-labeled ag-label-align-right ag-checkbox ag-input-field"
                >
                  <div
                    ref="eLabel"
                    class="ag-input-field-label ag-label ag-checkbox-label"
                  >
                    (Select All)
                  </div>
                  <div
                    ref="eWrapper"
                    class="ag-wrapper ag-input-wrapper ag-checkbox-input-wrapper ag-checked"
                    role="presentation"
                  >
                    <input
                      ref="eInput"
                      class="ag-input-field-input ag-checkbox-input"
                      type="checkbox"
                     
                      aria-labelledby="ag-187-label"
                    />
                  </div>
                </div>
              </div>
            </div>
            <div
              class="ag-virtual-list-item ag-filter-virtual-list-item"
              role="option"
              aria-setsize="111"
              aria-posinset="2"
              tabindex="-1"
              aria-checked="false"
              style="height: 24px; top: 24px;"
            >
              <div class="ag-set-filter-item">
                <!--AG-CHECKBOX-->
                <div
                  role="presentation"
                  ref="eCheckbox"
                  class="ag-set-filter-item-checkbox ag-labeled ag-label-align-right ag-checkbox ag-input-field"
                >
                  <div
                    ref="eLabel"
                    class="ag-input-field-label ag-label ag-checkbox-label"
                  >
                    Afghanistan
                  </div>
                  <div
                    ref="eWrapper"
                    class="ag-wrapper ag-input-wrapper ag-checkbox-input-wrapper ag-checked"
                    role="presentation"
                  >
                    <input
                      ref="eInput"
                      class="ag-input-field-input ag-checkbox-input"
                      type="checkbox"
                      aria-labelledby="ag-189-label"
                    />
                  </div>
                </div>
              </div>
            </div>
            

          </div>
          <div
            class="ag-tab-guard ag-tab-guard-bottom"
            role="presentation"
            tabindex="0"
          ></div>
        </div>
      </div>
    </div>
   
  </div>
</template>

<script>
import Vue from "vue";
export default Vue.extend({
  name: "nameFilter",
  data() {
    return {
      text: "",
      valueGetter: null,
    };
  },

  methods: {
    isFilterActive() {
      return this.text !== null && this.text !== undefined && this.text !== "";
    },

    doesFilterPass(params) {
      return (
        !this.text ||
        this.text
          .toLowerCase()
          .split(" ")
          .every((filterWord) => {
            return (
              this.valueGetter(params.node)
                .toString()
                .toLowerCase()
                .indexOf(filterWord) >= 0
            );
          })
      );
    },

    getModel() {
      return { values: [this.text], filterType: "set" };
    },

    setModel(model) {
      if (model) {
        this.text = model.value;
      }
    },

    afterGuiAttached() {
      this.$refs.eInput.focus();
    },

    componentMethod(message) {
      alert(`Alert from PartialMatchFilterComponent ${message}`);
    },
  },
  watch: {
    text: function(val, oldVal) {
      if (val !== oldVal) {
        this.params.filterChangedCallback();
      }
    },
  },
  created() {
    this.valueGetter = this.params.valueGetter;
  },
});
</script>

<style lang="scss"></style>
