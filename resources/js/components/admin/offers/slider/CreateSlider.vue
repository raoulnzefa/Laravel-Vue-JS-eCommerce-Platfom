<template>
  <div id="modal-form" class="modal fade">
    <div class="modal-dialog modal-custom">
      <div class="modal-content">
        <div class="modal-header">
          <h3 class="m-t-none m-b">Add Slider</h3>
          <button class="btn btn-default text-right" data-dismiss="modal">
            X
          </button>
        </div>
        <div class="modal-body">
          <form @submit.prevent="save()">
            <div class="row">
              <div
                class="col-md-12"
                v-if="validation_error"
                style="margin-top: 20px"
              >
                <div class="form-group">
                  <div>
                    <ul>
                      <li class="text-danger" v-for="error in validation_error">
                        {{ error[0] }}
                      </li>
                    </ul>
                  </div>
                </div>
              </div>
            </div>
            <div class="row">
              <div class="col-md-6">
                <div class="form-group">
                  <label>Slider Title*</label>
                  <input
                    type="text"
                    v-model="form.slider_title"
                    class="form-control"
                    placeholder="Slider Title"
                  />
                </div>
              </div>

              <div class="col-md-6">
                <div class="form-group">
                  <label>Slider Image (1920 X 420)</label
                  ><small>
                    Image best size is (1920 X 420), all image must be same
                    size.</small
                  >
                  <br />
                  <div
                    class="fileinput fileinput-new"
                    data-provides="fileinput"
                  >
                    <span class="btn btn-block btn-primary btn-file"
                      ><span class="fileinput-new"
                        ><i class="fa fa-camera"></i> Chose Image</span
                      >
                      <span class="fileinput-exists">Change Image</span
                      ><input type="file" name="..." @change="onImageChange"
                    /></span>

                    <img
                      style="height: 80px"
                      v-if="form.slider_banner"
                      :src="form.slider_banner"
                    />
                  </div>
                </div>
              </div>
              <div class="col-md-6">
                <div class="form-group">
                  <label>URL*</label>
                  <input
                    type="text"
                    v-model="form.back_url"
                    class="form-control"
                    placeholder="Slider Back Link URL"
                  />
                </div>
              </div>

              <div class="col-md-6">
                <div class="form-group">
                  <label>Slider Status*</label>

                  <select class="form-control" v-model="form.status">
                    <option value="1">Publish</option>
                    <option value="0">Not Publish</option>
                  </select>
                </div>
              </div>
            </div>

            <div class="row">
              <div class="col-md-12 text-right">
                <button type="submit" class="btn btn-primary">
                  {{ button_name }}
                </button>
                <button
                  type="close"
                  class="btn btn-default"
                  data-dismiss="modal"
                >
                  Close
                </button>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { EventBus } from "../../../../vue-assets";
import Mixin from "../../../../mixin";

export default {
  mixins: [Mixin],

  data() {
    return {
      form: {
        slider_title: "",
        slider_banner: "",
        back_url: "",
        description: "",
        status: 1,
      },

      button_name: "Save",
      validation_error: null,
      isLoading: false,
    };
  },

  methods: {
    // main feature image
    onImageChange(e) {
      let files = e.target.files || e.dataTransfer.files;
      if (!files.length) return;
      this.createImage(files[0]);
    },
    // creating main feature image
    createImage(file) {
      let reader = new FileReader();
      let vm = this;
      reader.onload = (e) => {
        vm.form.slider_banner = e.target.result;
      };
      reader.readAsDataURL(file);
    },

    save() {
      this.button_name = "Saving...";
      axios
        .post(base_url + "admin/slider", this.form)
        .then((response) => {
          if (response.data.status === "success") {
            $("#modal-form").modal("hide");
            this.resetForm();
            EventBus.$emit("slider-created");
            this.successMessage(response.data);
            this.button_name = "Save";
          } else {
            this.successMessage(response.data);
            this.button_name = "Save";
          }
        })
        .catch((err) => {
          if (err.response.status == 422) {
            this.validation_error = err.response.data.errors;

            this.validationError();

            this.button_name = "Save";
          } else {
            this.successMessage(err);

            // this.isloading = false;
            this.button_name = "Save";
          }
        });
    },

    resetForm() {
      this.form = {
        campaign_title: "",
        banner: "",
        meta_image: "",
        start_date: "",
        end_date: "",
        status: 1,

        product: [],
      };

      this.products = [];
      this.validation_error = null;
      this.isLoading = false;
    },
  },

  watch: {},
};
</script>

<style scoped="">
.modal-custom {
  max-width: 90% !important;
}

@media screen and (max-width: 573px) {
  .modal-custom {
    max-width: 100% !important;
    background-color: #000 !important;
  }
}
</style>