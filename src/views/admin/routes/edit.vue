<template>
  <section class="tables">
    <b-form @submit.prevent="updateRoute">
      <div class="row">
        <Breadcrumb :breadcrumbs="breadcrumbs" />
        <div class="col-lg-12 grid-margin stretch-card">
          <div class="card">
            <div class="card-body">
              <b-form-group
                label="Title"
                label-for="title-input"
                label-cols-sm="4"
                label-cols-lg="3"
                content-cols-sm
                content-cols-lg="7"
              >
                <b-form-input
                  id="title-input"
                  v-model.trim="$v.form.title.$model"
                  type="text"
                  placeholder="Enter title "
                  :class="{
                    'is-invalid': submitted && $v.form.title.$error,
                  }"
                  :state="validateState('title')"
                ></b-form-input>
                <div
                  v-if="submitted && !$v.form.title.required"
                  class="invalid-feedback"
                >
                  title is required
                </div>
              </b-form-group>
              <b-form-group
                label="Departure / Arrival stop"
                label-for="title-input"
                label-cols-sm="4"
                label-cols-lg="3"
                content-cols-sm
                content-cols-lg="7"
              >
                <!-- <v-select
                  @search="fetchLocations"
                  label="type"
                  :filterable="false"
                  :options="options"
                  v-model="form.stops[0].location"
                >
                  <template slot="no-options">
                    type to search locations..
                  </template>

                  <template slot="option" slot-scope="option">
                    <div class="d-center">
                      {{ option.title }}
                    </div>
                  </template>
                  <template slot="selected-option" slot-scope="option">
                    <div class="selected d-center">
                      {{ option.title }}
                    </div>
                  </template>
                </v-select> -->
                <multiselect
                  v-model.trim="form.stops[0].location"
                  :options="options"
                  placeholder="start typing to search Departure / Arrival stop."
                  label="title"
                  track-by="title"
                  :searchable="true"
                  :loading="submitted"
                  @search-change="fetchLocations"
                >
                  <template slot="option" slot-scope="props">
                    {{ props.option.title }}
                  </template>
                  <template slot="noOptions">
                    No Departure / Arrival stop found.</template
                  >
                  <template slot="noResult">
                    Departure / Arrival stop searched not matched.</template
                  >
                </multiselect>
              </b-form-group>
            </div>
          </div>
        </div>

        <div class="col-lg-12 grid-margin stretch-card">
          <div class="card">
            <div class="card-body">
              <table id="ferriRouteTable" class="table table-bordered">
                <thead>
                  <tr>
                    <th scope="col" class="col-md-5">Stops</th>
                    <!-- <th scope="col" class="col-md-3">Duration</th> -->
                    <th scope="col" class="col-md-3">Minimum Fare</th>
                    <th scope="col" class="col-md-4">Price per km</th>
                    <th></th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    id="fr_Route_122"
                    v-for="(data, k) in this.form.stops"
                    :key="k"
                  >
                    <td scope="row">
                      <div class="row">
                        <div class="col-sm-12">
                          <div class="form-group">
                            <!-- <v-select
                              @search="fetchPDLocations"
                              label="type"
                              :filterable="false"
                              :options="options"
                              v-model="data.location"
                            >
                              <template slot="no-options">
                                type to search locations..
                              </template>

                              <template slot="option" slot-scope="option">
                                <div class="d-center">
                                  {{ option.title }}
                                </div>
                              </template>
                              <template
                                slot="selected-option"
                                slot-scope="option"
                              >
                                <div class="selected d-center">
                                  {{ option.title }}
                                </div>
                              </template>
                            </v-select> -->
                            <multiselect
                              v-model.trim="data.location"
                              :options="options"
                              placeholder="start typing to search locations.."
                              label="title"
                              track-by="title"
                              :searchable="true"
                              :loading="submitted"
                              @search-change="fetchPDLocations"
                            >
                              <template slot="option" slot-scope="props">
                                {{ props.option.title }}
                              </template>
                              <template slot="noOptions">
                                No stop found.</template
                              >
                              <template slot="noResult">
                                Stop searched not matched.</template
                              >
                            </multiselect>
                          </div>
                        </div>
                      </div>
                    </td>
                    <!-- <td>      @select="selectPDLocation($event, k)"
                      <div class="row">
                        <label class="col-sm-3">if pick-up</label>
                        <div class="col-sm-6">
                          <input
                            type="number"
                            class="form-control"
                            v-model="data.duration_pickup"
                            value="1"
                          />
                        </div>
                        <label class="col-sm-3">mins</label>
                      </div>
                      <div class="row">
                        <label class="col-sm-3">if drop</label>
                        <div class="col-sm-6">
                          <input
                            type="number"
                            class="form-control"
                            v-model="data.duration_drop"
                            value="1"
                          />
                        </div>
                        <label class="col-sm-3">mins</label>
                      </div>
                    </td> -->
                    <td>
                      <div class="row">
                        <label class="col-sm-4">if pick-up</label>
                        <div class="col-sm-8">
                          <input
                            type="number"
                            class="form-control"
                            v-model="data.minimum_fare_pickup"
                            value="1"
                            min="1"
                          />
                        </div>
                      </div>
                      <div class="row">
                        <label class="col-sm-4">if drop</label>
                        <div class="col-sm-8">
                          <input
                            type="number"
                            class="form-control"
                            v-model="data.minimum_fare_drop"
                            value="1"
                            min="1"
                          />
                        </div>
                      </div>
                    </td>

                    <td>
                      <div class="row">
                        <label class="col-sm-3">if pick-up</label>
                        <div class="col-sm-9">
                          <div class="input-group">
                            <input
                              type="text"
                              v-model="data.price_per_km_pickup"
                              class="form-control"
                              aria-label="Amount (to the nearest rupee)"
                            />
                            <div class="input-group-append">
                              <span class="input-group-text">.00</span>
                            </div>
                          </div>
                        </div>
                      </div>

                      <div class="row">
                        <label class="col-sm-3">if drop</label>
                        <div class="col-sm-9">
                          <div class="input-group">
                            <input
                              type="text"
                              v-model="data.price_per_km_drop"
                              class="form-control"
                              aria-label="Amount (to the nearest rupee)"
                            />
                            <div class="input-group-append">
                              <span class="input-group-text">.00</span>
                            </div>
                          </div>
                        </div>
                      </div>
                    </td>

                    <td class="trashIconContainer">
                      <span v-if="k > 0">
                        <i
                          @click="removeRow(k, data)"
                          class="mdi mdi-delete-forever mdi-24px"
                        ></i>
                      </span>
                    </td>
                  </tr>
                </tbody>
              </table>
              <div class="d-flex justify-content-center">
                <input
                  name="addpd"
                  @click="addRow"
                  class="btn btn-outline-primary mt-4"
                  type="button"
                  value="+ Add pick-up / drop off location"
                />
              </div>
            </div>
          </div>
        </div>

        <div class="col-lg-12 grid-margin stretch-card">
          <div class="card">
            <div class="card-body">
              <b-form-group
                label="Status "
                label-for="status-input"
                invalid-feedback="status is required"
                class="mt-3"
                label-cols-sm="4"
                label-cols-lg="3"
                content-cols-sm
                content-cols-lg="7"
              >
                <b-form-radio-group
                  :options="statusOptions"
                  v-model.trim="$v.form.status.$model"
                  name="status"
                ></b-form-radio-group>

                <b-form-invalid-feedback
                  v-if="submitted && !$v.form.status.required"
                  >Please select one</b-form-invalid-feedback
                >
              </b-form-group>
              <br /><br />
              <b-form-group class="col-md-6 offset-md-5">
                <b-button
                  type="submit"
                  class="btn btn-success btn-lg text-center mr-2"
                >
                  <span
                    class="pl-2 spinner-border spinner-border-sm"
                    v-show="submitted"
                  >
                  </span
                  >Update</b-button
                >
                <b-button
                  :to="{ name: 'route' }"
                  class="btn btn-default btn-lg text-center"
                  >Back</b-button
                >
              </b-form-group>
            </div>
          </div>
        </div>
      </div>
    </b-form>
  </section>
</template>

<script>
import Breadcrumb from "../../../components/breadcrumb";
import { locationService, routeService } from "../../../services";
// import vSelect from "vue-select";
// import "vue-select/src/scss/vue-select.scss";
import { validationMixin } from "vuelidate";
import { required } from "vuelidate/lib/validators";
import Multiselect from "vue-multiselect";
import lodash from "lodash";

export default {
  mixins: [validationMixin],
  name: "routes",
  components: {
    Breadcrumb,
    Multiselect,
  },
  data() {
    return {
      breadcrumbs: {
        title: "Edit Route",
        b1: "Manage Routes",
        b2: "Routes",
        b3: "Index",
        link: true,
        name: "route",
      },
      submitted: false,
      loading: false,
      pds: [],
      options: [],
      form: {
        title: "",
        location: "",
        stops: [
          {
            id: "",
            location: "",
            minimum_fare_pickup: "",
            minimum_fare_drop: "",
            price_per_km_pickup: "",
            price_per_km_drop: "",
            stopId: "",
            stop_title: "",
          },
        ],
        status: "",
      },
      buses: [],
      statusOptions: [
        { text: "Active", value: true },
        { text: "Inactive", value: false },
      ],
    };
  },
  validations: {
    form: {
      title: { required },
      status: { required },
    },
  },
  methods: {
    addRow: function () {
      this.form.stops.push({
        id: "",
        location: [{}],
        duration_pickup: "",
        duration_drop: "",
        minimum_fare_pickup: "",
        minimum_fare_drop: "",
        price_per_km_pickup: "",
        price_per_km_drop: "",
      });
    },
    async removeRow(index, data) {
      try {
        var idx = this.form.stops.indexOf(data);
        console.log(idx, index);
        if (idx > -1) {
          if (data.id) {
            data.id != null ? await routeService.deleteRouteStop(data.id) : "";
          }
          this.form.stops.splice(idx, 1);
        }
      } catch (err) {
        this.$toast.open({
          message: err,
          type: "error",
          position: "top-right",
          duration: 4000,
        });
      }
    },
    validateState(name) {
      const { $dirty, $error } = this.$v.form[name];
      return $dirty ? !$error : null;
    },
    fetchLocations(search, loading) {
      if (search.length) {
        let type = "DA";
        this.search(loading, search, type, this);
      }
    },
    search: lodash.debounce(async (loading, search, type, vm) => {
      try {
        const response = await locationService.search({
          type: type,
          search: search,
        });
        vm.options = response.items;
      } catch (err) {
        this.$toast.open({
          message: err,
          type: "error",
          position: "top-right",
          duration: 5000,
        });
      }
    }, 350),
    fetchPDLocations(search, loading) {
      if (search.length) {
        let type = "PD";
        this.pdSearch(loading, search, type, this);
      }
    },
    // selectPDLocation(selectedOption, id) {
    //   console.log("selectedOption", selectedOption, id);
    //   this.form.stops[id] = {
    //     stopId: selectedOption.id,
    //     stop_name: selectedOption.title,
    //     location: selectedOption,
    //   };
    // },
    pdSearch: lodash.debounce(async (loading, search, type, vm) => {
      try {
        const response = await locationService.search({
          type: type,
          search: search,
        });
        vm.options = response.items;
      } catch (err) {
        this.$toast.open({
          message: err,
          type: "error",
          position: "top-right",
          duration: 5000,
        });
      }
    }, 350),
    async getRoute() {
      try {
        const response = await routeService.find(this.$route.params.id);
        if (response.status) {
          this.form = response.data;
        }
      } catch (e) {
        console.log("params", e);
        this.$toast.open({
          message: e,
          type: "error",
          position: "top-right",
          duration: 5000,
        });
      }
    },
    async updateRoute() {
      try {
        this.submitted = true;
        // stop here if form is invalid
        this.$v.$touch();
        if (this.$v.$invalid) {
          return;
        }

        const response = await routeService.update(
          this.$route.params.id,
          this.form
        );
        if (response.status) {
          this.submitted = false;
          this.$toast.open({
            message: response.message,
            type: "success",
            position: "top-right",
            duration: 2000,
            // all of other options may go here
          });
          setTimeout(
            () =>
              this.$router.push({
                path: `/routes`,
              }),
            2000
          );
        }
      } catch (e) {
        this.$toast.open({
          message: e,
          type: "error",
          position: "top-right",
          duration: 5000,
        });
      }
    },
  },
  mounted() {
    this.getRoute();
  },
};
</script>

<style scoped>
.trashIconContainer i {
  color: #e74c3c;
  cursor: pointer;
}
.d-center {
  display: flex;
  align-items: center;
}
/* .v-select .dropdown li {
  border-bottom: 1px solid rgba(112, 128, 144, 0.1);
}

.v-select .dropdown li:last-child {
  border-bottom: none;
}

.v-select .dropdown li a {
  padding: 10px 20px;
  width: 100%;
  font-size: 1.25em;
  color: #3c3c3c;
}

.v-select .dropdown-menu .active > a {
  color: #fff;
}
.vs__dropdown-toggle {
  padding: 0 0 10px 0 !important;
}
.vs__actions {
  padding: 13px 6px 0 3px !important;
}
.vs__selected {
  margin: 14px 2px 0px 2px !important;
} */
</style>
<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
