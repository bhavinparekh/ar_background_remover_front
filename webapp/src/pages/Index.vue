<template>
  <div class="row justify-center items-center q-mt-xl">
    <div class="col-10 text-center">
      <p class="text-h6">Background remover interface</p>
        <!-- --------------------- MOBILE ONLY  (TAKE A PICTURE) ----------------------------------------->
        <div :class="$q.screen.lt.md? 'q-my-xl row justify-center' :'q-pa-md q-my-lg'">
          <div :class="$q.screen.lt.md? 'col-10 col-sm-10 col-xs-12' : 'q-gutter-md'">
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
          <div v-if="previewPicture.length < 1" :class="$q.screen.lt.md? 'text-center col-10 col-sm-7 col-xs-10 q-my-lg' : 'col-12 q-my-lg row justify-center items-center'">
            <div v-for="(image, index) in previewPicture" :key="index">
              <img
                class=""
                :style="$q.screen.lt.md? 'width : 100%' : 'width : 30%'"
                :src="image[index]"
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
                v-if="updatedPicture.length < 1"
                class=""
                :style="$q.screen.lt.md? 'width : 100%' : 'width : 30%'"
                :src="updatedPicture[index]"
                alt="after ..."
              />
            </div>
          </div>
        </div>
        <!-- ---------------------  END MOBILE ONLY -------------------------------------->
      <!------------------------------------- ADD A PICTURE ------------------------------------------>
<!--         <div v-else class="q-pa-md q-my-lg">
        <div class="q-gutter-md">
          <q-file
            filled
            v-model="picture"
            accept=".jpg, image/*"
            label="Choose a picture"
            @change="showPreview"
            counter
          >
            <template v-slot:append>
              <q-btn round dense flat icon="add" @click.stop />
            </template>
          </q-file>
         <div v-if="previewPicture" class="col-12 q-my-lg row justify-center items-center">
          <img
            class=""
            style="width : 30%"
            :src="previewPicture"
            alt="before ..."
          />
          <q-btn
            size="md"
            color="primary"
            label="send"
            class="q-mx-lg"
            :disabled="picture === null ? true : false"
            @click="sendPicture"
          />
          <img
            v-if="updatedPicture"
            class=""
            style="width : 30%"
            :src="updatedPicture"
            alt="after ..."
          />
        </div>  
        </div>
      </div> -->
      <!------------------------------------------- END ---------------------------------------->

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
      previewPicture: [],
      updatedPicture: []
    };
  },

  updated() {
    if (this.picture) {
      this.showPreview();
    }
  },

  methods: {
    showPreview() {
      console.log('INSIDE PREVIEW')
      const reader = new FileReader();
      reader.addEventListener(
        "load",
        () => {
          this.previewPicture.push(reader.result);
        },
        false
      );
      reader.readAsDataURL(this.picture);
      console.log('PREVIEW done', this.previewPicture)
/*       if (this.updatedPicture) {
        this.updatedPicture = null;
      } */
    },

    showResult(picture) {
      const reader = new FileReader();
      reader.addEventListener(
        "load",
        () => {
          this.updatedPicture.push(reader.result);
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
    }
  }
};
</script>
