<template>
  <div class="row justify-center items-center q-mt-xl">
    <div class="col-10 text-center">
      <p class="text-h6">Background remover interface</p>
      <!------------------------------------- ADD A PICTURE ------------------------------------------>

      <!------------------------------------------- END ---------------------------------------->

      <!-- --------------------- MOBILE ONLY  (TAKE A PICTURE) ----------------------------------------->
      <div v-if="$q.screen.lt.md" class="q-my-xl row">
        <div class="col-12">
          <q-file
            filled
            v-model="picture"
            accept=".jpg, image/*"
            label="Choose a picture"
            @change="showPreview"
            counter
          >
            <template v-slot:prepend>
              <q-icon name="add_a_photo" @click.stop />
            </template>
          </q-file>
        </div>
        <div v-if="previewPicture" class="col-12 q-my-lg">
          <img
            class=""
            style="width : 100%"
            :src="previewPicture"
            alt="before ..."
          />
          <q-btn
            size="sm"
            color="primary"
            label="send"
            :disabled="picture === null ? true : false"
            @click="sendPicture"
          />

          <img
            v-if="updatedPicture"
            class=""
            style="width : 100%"
            :src="updatedPicture"
            alt="after ..."
          />
        </div>
      </div>

      <!-- ---------------------  END MOBILE ONLY -------------------------------------->
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "PageIndex",
  data() {
    return {
      picture: null,
      previewPicture: null,
      updatedPicture: null
    };
  },

  updated() {
    if (this.picture) {
      this.showPreview();
    }
  },

  methods: {
    showPreview() {
      const reader = new FileReader();
      reader.addEventListener(
        "load",
        () => {
          this.previewPicture = reader.result;
        },
        false
      );
      reader.readAsDataURL(this.picture);
      if (this.updatedPicture) {
        this.updatedPicture = null;
      }
    },

    showResult(picture) {
      const reader = new FileReader();
      reader.addEventListener(
        "load",
        () => {
          this.updatedPicture = reader.result;
        },
        false
      );
      reader.readAsDataURL(picture);
      //reset picture
      this.picture = null;
    },

    sendPicture() {
      let formData = new FormData();
      formData.append("uploaded_file", this.picture);
      formData.append("seller_url", "http://0.0.0.0:8080");

      axios
        .post("http://0.0.0.0:8000/api/v1/removebg/", formData, {
          headers: { "Content-Type": "multipart/form-data" },
          responseType: "blob"
        })
        .then(data => {
          //console.log(data);
          if (data.status === 200) {
            this.showResult(data.data);
            this.$q.notify({
              message: "The background was successfully removed",
              color: "green"
            });
          } else {
            this.$q.notify({
              message: "The background could not be removed, please try again",
              color: "red"
            });
          }
        })
        .catch(err => console.error(err));
    }
  }
};
</script>
