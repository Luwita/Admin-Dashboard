<template>
  <section class="tables">
    <div class="row">
      <Breadcrumb :breadcrumbs="breadcrumbs" />
      <div class="col-lg-12">
        <vue-bootstrap4-table
          :rows="rows"
          :columns="columns"
          :config="config"
          @on-change-query="onChangeQuery"
          @refresh-data="onRefreshData"
          :total-rows="total_rows"
          :classes="classes"
          :show-loader="showLoader"
        >
          <template slot="global-search-clear-icon">
            <i class="mdi mdi-account-search"></i>
          </template>

          <template slot="refresh-button-text">
            <i class="mdi mdi-sync-alert"></i> Refresh
          </template>
          <template slot="reset-button-text">
            <i class="mdi mdi-broom"></i> Reset filters
          </template>

          <template slot="status" slot-scope="props">
            <span v-if="props.column.name == 'status'">
              <b-badge variant="success" v-if="props.row.status == 'Active'"
                >Active</b-badge
              >
              <b-badge variant="danger" v-if="props.row.status == 'Inactive'"
                >Inactive</b-badge
              >
            </span>
          </template>
          <template slot="action" slot-scope="props">
            <span v-if="props.column.name == 'action'">
              <b-dropdown
                id="dropdown-left"
                text="Actions"
                variant="outline-info"
                class="m-2"
              >
                <b-dropdown-item @click="viewRow(props.row)">
                  <span class="text-dark">
                    <i class="mdi mdi-eye"></i> View
                  </span></b-dropdown-item
                >
                <b-dropdown-item :href="'#/location/' + props.row.ids"
                  ><span class="text-primary">
                    <i class="mdi mdi-pencil"></i> Edit
                  </span></b-dropdown-item
                >
                <b-dropdown-item
                  @click.stop="deleteRow(props.row.ids, props.row.title)"
                  ><span class="text-danger">
                    <i class="mdi mdi-delete"></i> Delete
                  </span></b-dropdown-item
                >
              </b-dropdown>
            </span>
          </template>

          <template slot="paginataion-previous-button"> Previous </template>
          <template slot="paginataion-next-button"> Next </template>
          <template slot="vbt-action-buttons">
            <div
              class="btn-group float-right"
              role="group"
              aria-label="Basic example"
            >
              <download-excel
                class="btn btn-success"
                :data="excelDownload"
                :name="excelName"
              >
                Excel <i class="mdi mdi-file-excel-box"></i>
              </download-excel>
            </div>
          </template>
        </vue-bootstrap4-table>

        <!---START EDIT Modal--->
        <b-modal
          ref="myModalRefLocation"
          :title="title"
          size="md"
          :ok-title="modaloktitle"
          @hidden="hideModal"
          @ok="handleOk"
        >
          <span v-if="modalView == true"> <modalView :form="form" /></span>
        </b-modal>
        <!--END EDIT modal--->
      </div>
    </div>
  </section>
</template>

<script>
import Breadcrumb from "../../../components/breadcrumb";
import { locationService } from "../../../services";
import VueBootstrap4Table from "vue-bootstrap4-table";
import moment from "moment-timezone";
import downloadExcel from "vue-json-excel";
import modalView from "./modelView";

export default {
  name: "location",
  data() {
    return {
      breadcrumbs: {
        title: "Stops",
        b1: "Manage Routes",
        b2: "Stops",
        b3: "Index",
        link: false,
        name: "Stop lists",
      },
      showLoader: false,
      columns: [
        {
          label: "Title",
          name: "title",
          sort: true,
        },
        {
          label: "Type",
          name: "type",
          sort: true,
          filter: {
            type: "select",
            mode: "type",
            closeDropdownOnSelection: true,
            placeholder: "Select type",
            options: [
              {
                name: "Departure / Arrival location",
                value: "DA",
              },
              {
                name: "Pick-up / Drop off location",
                value: "PD",
              },
            ],
          },
        },
        {
          label: "Status",
          name: "status",
          sort: true,
          filter: {
            type: "select",
            mode: "status",
            closeDropdownOnSelection: true,
            placeholder: "Select status",
            options: [
              {
                name: "Active",
                value: true,
              },
              {
                name: "Inactive",
                value: false,
              },
            ],
          },
        },
        { label: "Action", name: "action", sort: false },
      ],
      rows: [],
      config: {
        server_mode: true, // by default false
        loaderText: "Updating...", // by default 'Loading...'
        pagination: true,
        global_search: {
          placeholder: "Enter search location",
          visibility: true,
          case_sensitive: false,
          showClearButton: false,
          searchOnPressEnter: false,
          searchDebounceRate: 5000,
        },
        per_page_options: [10, 20, 30, 50, 100],
        highlight_row_hover_color: "silver",
        highlight_row_hover: true,
        card_mode: true,
      },
      dropdowns: [],
      classes: {
        table: "table-bordered table-striped",
      },
      queryParams: {
        sort: [],
        filters: [],
        global_search: "",
        per_page: 10,
        page: 1,
      },
      total_rows: 0,
      modalEdit: false,
      modalView: false,
      modaloktitle: "",
      title: "",
    };
  },
  components: {
    Breadcrumb,
    downloadExcel,
    VueBootstrap4Table,
    modalView,
  },
  computed: {
    excelDownload() {
      return locationService.tranform(this.rows);
    },
    excelName() {
      return this.breadcrumbs.title + "_" + moment().local().unix();
    },
  },
  methods: {
    viewRow(data) {
      this.title = "Location Details";
      this.modalView = true;
      this.modalEdit = false;
      this.modaloktitle = "Ok";
      this.$refs.myModalRefLocation.show();
      this.form = data;
    },
    deleteRow(id, name) {
      try {
        this.$swal
          .fire({
            title: `Are you sure you want to delete ${name}?`,
            text: "You won't be able to revert this!",
            icon: "warning",
            showCancelButton: true,
            confirmButtonColor: "#3085d6",
            cancelButtonColor: "#d33",
            confirmButtonText: "Yes, delete it!",
          })
          .then((result) => {
            if (result.isConfirmed) {
              locationService.deleteLocation(id).then((response) => {
                if (
                  typeof response.data === "object" &&
                  response.data.status === 403
                ) {
                  this.$swal.fire(response.data.message, "", "error");
                  this.$toast.open({
                    message: response.data.message,
                    type: "error",
                    position: "top-right",
                    duration: 3000,
                    // all of other options may go here
                  });
                  this.showLoader = false;
                } else if (response.status) {
                  this.$swal.fire("Deleted!", response.message, "success");
                  this.$toast.open({
                    message: response.message,
                    type: "success",
                    position: "top-right",
                    duration: 3000,
                    // all of other options may go here
                  });
                  this.showLoader = true;
                  this.loadItems();
                } else {
                  this.$swal.fire("Failed!", response.message, "danger");
                  this.$toast.open({
                    message: response.message,
                    type: "danger",
                    position: "top-right",
                    duration: 3000,
                    // all of other options may go here
                  });
                  this.showLoader = true;
                  this.loadItems();
                }
              });
            }
          });
      } catch (e) {
        this.$toast.open({
          message: e,
          type: "error",
          position: "top-right",
          duration: 5000,
        });
      }
    },
    editRow(data) {
      this.title = "Location Edit";
      this.modalEdit = true;
      this.modalView = false;
      this.modaloktitle = "Update";
      this.$refs.myModalRefLocation.show();
      console.log(data);
      this.form = data;
    },
    hideModal() {
      this.$refs.myModalRefLocation.hide();
      this.modalEdit = false;
      this.modalView = false;
    },
    handleOk() {
      // Prevent modal from closing
      this.$refs.myModalRefLocation.hide();
      this.modalEdit = false;
      this.modalView = false;
      // Trigger submit handler
    },
    updateParams(newProps) {
      this.serverParams = Object.assign({}, this.serverParams, newProps);
    },
    onChangeQuery(queryParams) {
      this.queryParams = queryParams;
      this.showLoader = true;
      this.loadItems();
    },
    onRefreshData() {
      this.showLoader = true;
      this.loadItems();
    },
    // load items is what brings back the rows from server
    loadItems() {
      locationService.getAll(this.queryParams).then((response) => {
        this.total_rows = response.data.totalRecords;
        this.rows = response.data.locations;
        this.showLoader = false;
      });
    },
    enableDropdowns() {
      this.$el
        .querySelectorAll('[data-toggle="dropdown"]')
        .forEach(($dropdownToggle) => {
          const $dropdown = $dropdownToggle.nextElementSibling;
          let isShown = false;

          function setIsShown(state) {
            isShown = state;
            $dropdown.classList.toggle("show", isShown);
          }

          if (!this.dropdowns.includes($dropdown)) {
            this.dropdowns.push($dropdown);

            $dropdownToggle.addEventListener("click", (event) => {
              event.preventDefault();
              setIsShown(!isShown);
            });

            $dropdown.addEventListener("click", (event) => {
              event.preventDefault();
              setIsShown(false);
            });

            $dropdown.clickOutsideEvent = (event) => {
              const isDropdownOrChildren =
                $dropdown === event.target || $dropdown.contains(event.target);
              const isDropdownToggleOrChildren =
                $dropdownToggle === event.target ||
                $dropdownToggle.contains(event.target);

              if (!isDropdownOrChildren && !isDropdownToggleOrChildren) {
                setIsShown(false);
              }
            };
            document.addEventListener("click", $dropdown.clickOutsideEvent);
          }
        });
    },
  },
  mounted() {
    this.loadItems();
  },
  updated() {
    this.enableDropdowns();
  },
  destroyed() {
    this.dropdowns.forEach(($dropdown) => {
      document.removeEventListener("click", $dropdown.clickOutsideEvent);
    });
  },
};
</script>

<style></style>
