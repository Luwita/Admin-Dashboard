<template>
  <div>
    <section class="tables">
      <b-container>
        <b-row>
          <b-col cols="4">Full Name</b-col>
          <b-col cols="8"
            >{{ modalData.firstname }} {{ modalData.lastname }}</b-col
          >
          <b-col cols="4">Email Address</b-col>
          <b-col cols="8">{{ modalData.email }}</b-col>
          <b-col cols="4">Phone</b-col>
          <b-col cols="8">{{ modalData.phone }}</b-col>
          <b-col cols="4">Status</b-col>
          <b-col cols="8">{{ modalData.status }}</b-col>
          <b-col cols="4">Profile Picture</b-col>
          <b-col cols="8"
            ><b-img width="60" height="60" :src="modalData.picture"></b-img
          ></b-col>
          <b-col cols="12"
            ><p><b class="text-uppercase mt-3 mb-3">Documents</b></p></b-col
          >
          <b-col cols="4">Profile Picture</b-col>
          <b-col cols="8"
            ><b-img
              class="img-fluid mt-2"
              width="350"
              height="200"
              :src="modalData.document_licence"
            ></b-img
          ></b-col>
          <b-col cols="4">Licence</b-col>
          <b-col cols="8"
            ><b-img
              class="img-fluid mt-2"
              width="350"
              height="200"
              :src="modalData.document_licence"
            ></b-img
          ></b-col>
          <b-col cols="4">Adhar Card</b-col>
          <b-col cols="8"
            ><b-img
              class="img-fluid"
              width="350"
              height="200"
              :src="modalData.document_national_icard"
            ></b-img
          ></b-col>
          <b-col cols="4">Police vertification</b-col>
          <b-col cols="8"
            ><b-img
              class="img-fluid"
              width="350"
              height="200"
              :src="modalData.document_police_vertification"
            ></b-img
          ></b-col>
          <b-col cols="4">Created At</b-col>
          <b-col cols="8">{{ dateConvert(modalData.createdAt) }}</b-col>
        </b-row>
      </b-container>
    </section>
  </div>
</template>

<script>
import moment from "moment-timezone";

export default {
  name: "modelView",
  props: ["modalData"],
  methods: {
    dateConvert(data) {
      return moment.utc(data).tz("Asia/Kolkata").format("LLL");
    },
  },
};
</script>

<style></style>
