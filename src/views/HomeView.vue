<template>
  <v-container style="display: flex; flex-direction: column; align-items: center;  margin-top: 12px; margin-bottom: 12px">
    <!--Carousels Management-->
    <v-row style="width: 80%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
      <div style="margin-bottom: 12px">Carousels Management</div>
      <v-expansion-panels>
        <v-expansion-panel
          v-for="(carousel, i) in carousels"
          :key=i
          @click="cancelImageChange()"
        >
          <v-expansion-panel-header>
            {{ i + 1 }}
          </v-expansion-panel-header>
          <v-expansion-panel-content>
            <v-card>
              <v-img :src=carousel.source></v-img>
              <div>{{ carousel.link }}</div>
            </v-card>
            <div style="margin-top: 12px; display: flex; flex-direction: row; justify-content: center;">
              <v-btn style="margin-right: 24px" @click="editCarousel(carousel)">EDIT</v-btn>
              <v-btn @click="deleteCarousel(carousel.image_id)">DELETE</v-btn>
            </div>
            <div v-if="edit_carousel" style="width: 100%; display: flex; flex-direction: column;">
              <!--`action`: type of mutation; `api`: assign api dynamically; `code`: defines which group of images to change; `id`: only for edit by id, not for add.-->
              <ImageChangeComponent :action="'edit'" :api="'cms/carousels/'" :code="1" :id="carousel.image_id" @ImageStatusChange="handleImageStatusChange"/>
              <div style="width: 100%; display: flex; flex-direction: row; justify-content: center;">
                <v-btn @click="cancelImageChange()">CANCEL</v-btn>
              </div>
            </div>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
      <div style="margin-top: 12px">
        <v-btn @click="addCarousel()">ADD</v-btn>
      </div>
      <div v-if="add_carousel" style="width: 100%; display: flex; flex-direction: column;">
        <!--No slash at the end of restful api for add.-->
        <ImageChangeComponent :action="'add'" :api="'cms/carousels'" :code="1" @ImageStatusChange="handleImageStatusChange"/>
        <div style="width: 100%; display: flex; flex-direction: row; justify-content: center;">
          <v-btn @click="cancelImageChange()">CANCEL</v-btn>
        </div>
      </div>
    </v-row>
    <!--Flyer Management-->
    <v-row style="width: 80%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
      <div style="margin-top: 12px; margin-bottom: 12px">Flyer Management</div>
      <v-expansion-panels>
        <v-expansion-panel
          v-for="(flyer, i) in flyers"
          :key=i
          @click="cancelImageChange()"
        >
          <v-expansion-panel-header>
            {{ i + 1 }}
          </v-expansion-panel-header>
          <v-expansion-panel-content>
            <v-card>
              <v-img :src=flyer.source></v-img>
              <div>{{ flyer.link }}</div>
            </v-card>
            <div style="margin-top: 12px; display: flex; flex-direction: row; justify-content: center;">
              <v-btn style="margin-right: 24px" @click="editFlyer(flyer)">EDIT</v-btn>
              <!--v-btn @click="deleteFlyer(flyer.id)">DELETE</v-btn-->
            </div>
            <div v-if="edit_flyer" style="width: 100%; display: flex; flex-direction: column;">
              <!--`action`: type of mutation; `api`: assign api dynamically; `code`: defines which group of images to change; `id`: only for edit by id, not for add.-->
              <ImageChangeComponent :action="'edit'" :api="'cms/flyers/'" :code="2" :id="flyer.image_id" @ImageStatusChange="handleImageStatusChange"/>
              <div style="width: 100%; display: flex; flex-direction: row; justify-content: center;">
                <v-btn @click="cancelImageChange()">CANCEL</v-btn>
              </div>
            </div>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
    </v-row>
  </v-container>
</template>

<script>
  import apiClient from "@/api.js"
  import ImageChangeComponent from "@/components/ImageChangeComponent.vue"
  export default {
    name: "HomeView",
    components: { ImageChangeComponent },
    mounted() {
      this.initializeCarousels(),
      this.initializeFlyers()
    },
    data() {
      return {
        add_carousel: false,
        edit_carousel: false,
        edit_flyer: false,
        carousels: [],
        flyers: []
      };
    },
    methods: {
      initializeCarousels() {
        apiClient.get("cms/carousels").then(response => {
          this.carousels = response.data.carousels
          this.edit_carousel = false
          this.add_carousel = false
        })
      },
      initializeFlyers() {
        apiClient.get("cms/flyers").then(response => {
          this.flyers = response.data.flyers
          this.edit_flyer = false
        })
      },
      // Binded with child component's this.$emit.
      // This val does not need to be defined manually.
      // `code` from child controls which initialize finction to call.
      handleImageStatusChange(code) {
        if ( code === 1 ) {
          this.initializeCarousels()
        } else if ( code === 2 ) {
          this.initializeFlyers()
        }
      },
      addCarousel() {
        this.add_carousel = true
        this.edit_carousel = false
      },
      editCarousel(carousel) {
        this.add_carousel = false
        this.edit_carousel = true
        this.edit_flyer = false
      },
      deleteCarousel(carousel_id) {
        apiClient.delete("cms/carousels/" + carousel_id, {
            src: this.carousel_src,
            link: this.carousel_link
        }).then(response => {
            console.log(response);
            this.initializeCarousels();
        }).catch(error => {
            console.log(error);
            // 请求失败时，你可能想要做一些事情
        });
      },
      editFlyer(flyer) {
        this.add_carousel = false
        this.edit_carousel = false
        this.edit_flyer = true
      },
      cancelImageChange() {
        this.add_carousel = false
        this.edit_carousel = false
        this.edit_flyer = false
      }
    }
  }
</script>
