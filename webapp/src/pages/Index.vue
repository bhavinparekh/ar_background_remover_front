<template>
  <div class="row justify-center items-center q-mt-xl">
    <div class="col-10 text-center">
      <p class="text-h6">Background remover interface</p>
      <!------------------------------------- ADD A PICTURE ------------------------------------------>

      <!------------------------------------------- END ---------------------------------------->

      <!-- --------------------- MOBILE ONLY  (TAKE A PICTURE) ----------------------------------------->
      <div v-if="$q.screen.lt.md">
        Mobile
      </div>

      <!-- ---------------------  END MOBILE ONLY -------------------------------------->
    </div>
  </div>
</template>

<script>
export default {
  name: "PageIndex"
};
</script>
