<template>
  <q-page>
    <div class="row justify-center items-center q-mt-xl">
      <div class="col-10 text-center">
        <p class="text-h6">Background remover interface</p>

        <div
          :class="
            $q.screen.lt.md ? 'q-my-xl row justify-center' : 'q-pa-md q-my-lg'
          "
        >
          <div
            :class="
              $q.screen.lt.md ? 'col-10 col-sm-10 col-xs-12' : 'q-gutter-md'
            "
          >
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

          <!--<div v-if="previewPicture" :class="$q.screen.lt.md? 'text-center col-10 col-sm-7 col-xs-10 q-my-lg' : 'col-12 q-my-lg row justify-center items-center'">
            
              <img
                class=""
                :style="$q.screen.lt.md? 'width : 100%' : 'width : 30%'"
                :src="previewPicture"
                alt="before ..."
              />
              <q-btn
                size="md"
                :class="$q.screen.lt.md? 'q-my-lg' : 'q-mx-lg'"
                color="primary"
                label="send"
                :disabled="picture === null ? true : false"
                @click="sendPicture"
              />

              <img
                v-if="updatedPicture"
                class=""
                :style="$q.screen.lt.md? 'width : 100%' : 'width : 30%'"
                :src="updatedPicture"
                alt="after ..."
              />
            
          </div>-->
          <div class="row justify-center">
            <div
              v-if="arrayPictureUpdated.length > 0"
              class="column justify-center items-center col-lg-6 col-md-7 col-sm-10 col-xs-12 shadow-5 q-pa-md q-mt-sm"
            >
              <span class="text-h6">Final Picture</span>
              <span class="text-caption q-mb-sm"
                >Choose a picture to dowload by clicking on it</span
              >
              <div
                v-if="pictureToDownload"
                class="row justify-center items-center"
              >
                <img
                  :class="
                    $q.screen.lt.md
                      ? 'col-sm-4 col-xs-6 '
                      : 'col-lg-5 col-md-4 q-mx-md'
                  "
                  :style="
                    $q.screen.lt.sm
                      ? 'max-height: 200px; max-width:100%'
                      : $q.screen.lt.md
                      ? 'max-height: 250px; max-width:100%'
                      : 'max-height: 250px; max-width:100%'
                  "
                  :src="pictureToDownload"
                  alt="download ..."
                />
                <q-btn
                  size="md"
                  :class="$q.screen.lt.md ? 'q-mx-md col-4' : 'q-mx-lg col-3'"
                  color="red"
                  label="Download"
                />
              </div>
            </div>
          </div>

          <div
            v-for="(image, index) in arrayPicturePreview"
            :key="index"
            :class="
              $q.screen.lt.md
                ? 'text-center col-md-10 col-sm-12 col-xs-10 q-my-lg'
                : 'col-12 q-my-lg row justify-center items-center'
            "
          >
            <img
              :class="$q.screen.lt.md ? 'q-mx-md' : 'q-mx-lg'"
              :style="
                $q.screen.lt.md
                  ? 'max-height: 180px; max-width:100%'
                  : 'max-height: 250px; max-width:30%'
              "
              :src="image"
              alt="before ..."
              @click="chooseDowload(image)"
            />
            <q-btn
              v-if="!arrayPictureUpdated[index]"
              size="md"
              :class="$q.screen.lt.md ? 'q-ma-lg' : 'q-mx-lg'"
              color="primary"
              label="send"
              :disabled="picture === null ? true : false"
              @click="sendPicture"
            />

            <img
              v-if="arrayPictureUpdated[index]"
              :class="$q.screen.lt.md ? 'q-mx-md' : 'q-mx-lg'"
              :style="
                $q.screen.lt.md
                  ? 'max-height: 180px; max-width:100%'
                  : 'max-height: 250px; max-width:30%'
              "
              :src="arrayPictureUpdated[index]"
              alt="after ..."
              @click="chooseDowload(arrayPictureUpdated[index])"
            />
          </div>
        </div>
      </div>
    </div>
    <q-page-scroller
      position="bottom-right"
      :scroll-offset="150"
      :offset="[25, 25]"
    >
      <q-btn fab icon="keyboard_arrow_up" color="secondary" />
    </q-page-scroller>
  </q-page>
</template>

<script>
import axios from "axios";

export default {
  name: "PageIndex",
  data() {
    return {
      picture: null,
      previewPicture: null,
      updatedPicture: null,
      arrayPicturePreview: [],
      arrayPictureUpdated: [],
      pictureToDownload: null
    };
  },

  updated() {
    if (this.picture) {
      this.showPreview();
    }
  },

  methods: {
    showPreview() {
      //console.log("INSIDE PREVIEW");
      if (!this.arrayPicturePreview.includes(this.previewPicture)) {
        const reader = new FileReader();
        reader.addEventListener(
          "load",
          () => {
            this.previewPicture = reader.result;
            this.arrayPicturePreview.push(this.previewPicture);
          },
          false
        );
        reader.readAsDataURL(this.picture);
        //console.log("PREVIEW done", this.previewPicture);
      }
    },

    showResult(picture) {
      const reader = new FileReader();
      reader.addEventListener(
        "load",
        () => {
          this.updatedPicture = reader.result;
          this.arrayPictureUpdated.push(this.updatedPicture);
        },
        false
      );
      reader.readAsDataURL(picture);
      //reset picture
      this.previewPicture = null;
      this.picture = null;
    },

    sendPicture() {
      let formData = new FormData();
      formData.append("uploaded_file", this.picture);
      formData.append("seller_url", "http://0.0.0.0:8080");

      axios
        .post("http://0.0.0.0:7000/api/v1/removebg/", formData, {
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
    },

    chooseDowload(picture) {
      this.pictureToDownload = picture;
    }
  }
};
</script>
