<template>
  <div class0="container-scroller">
    <div class="container-fluid page-body-wrapper full-page-wrapper">
      <div
        class="content-wrapper d-flex align-items-center text-center error-page bg-primary"
      >
        <div class="row flex-grow">
          <div class="col-lg-7 mx-auto text-white">
            <div class="row align-items-center d-flex flex-row">
              <div class="col-lg-6 text-lg-right pr-lg-4">
                <h1 class="display-1 mb-0">403</h1>
              </div>
              <div class="col-lg-6 error-page-divider text-lg-left pl-lg-4">
                <h2>SORRY!</h2>
                <h3 class="font-weight-light">
                  The page you’re looking for was not Forbidden
                </h3>
              </div>
            </div>
            <div class="row mt-5">
              <div class="col-12 text-center mt-xl-2">
                <router-link
                  to="/auth/login"
                  class="text-white font-weight-medium"
                >
                  Back to Login
                </router-link>
              </div>
            </div>
            <div class="row mt-5">
              <div class="col-12 mt-xl-2">
                <p class="text-white font-weight-medium text-center">
                  Copyright &copy; {{ this.$year }} All rights reserved.
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <!-- content-wrapper ends -->
</template>

<script>
export default {
  name: "error_403",
};
</script>

<style lang="scss" scoped></style>
