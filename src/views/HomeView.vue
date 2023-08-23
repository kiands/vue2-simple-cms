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
              <ImageChangeComponent :action="'edit'" :code="1" :id="carousel.image_id" @Submission="handleImageStatusChange"/>
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
        <ImageChangeComponent :action="'add'" :code="1" @Submission="handleImageStatusChange"/>
        <div style="width: 100%; display: flex; flex-direction: row; justify-content: center;">
          <v-btn @click="cancelImageChange()">CANCEL</v-btn>
        </div>
      </div>
    </v-row>
    <!--Flyer Management-->
    <v-row style="width: 80%; display: flex; flex-direction: column; align-items: center; justify-content: center; margin-bottom: 12px">
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
              <ImageChangeComponent :action="'edit'" :code="2" :id="flyer.image_id" @Submission="handleImageStatusChange"/>
              <div style="width: 100%; display: flex; flex-direction: row; justify-content: center;">
                <v-btn @click="cancelImageChange()">CANCEL</v-btn>
              </div>
            </div>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
    </v-row>
    <!--Sponsor Management-->
    <v-row style="width: 80%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
      <div style="margin-bottom: 12px">Sponsor Management</div>
      <v-expansion-panels>
        <v-expansion-panel
          v-for="(sponsor, i) in sponsors"
          :key=i
          @click="cancelImageChange()"
        >
          <v-expansion-panel-header>
            {{ i + 1 }}
          </v-expansion-panel-header>
          <v-expansion-panel-content>
            <v-card>
              <v-img :src=sponsor.source></v-img>
              <div>{{ sponsor.link }}</div>
            </v-card>
            <div style="margin-top: 12px; display: flex; flex-direction: row; justify-content: center;">
              <v-btn style="margin-right: 24px" @click="editSponsor(sponsor)">EDIT</v-btn>
              <v-btn @click="deleteSponsor(sponsor.image_id)">DELETE</v-btn>
            </div>
            <div v-if="edit_sponsor" style="width: 100%; display: flex; flex-direction: column;">
              <!--`action`: type of mutation; `api`: assign api dynamically; `code`: defines which group of images to change; `id`: only for edit by id, not for add.-->
              <ImageChangeComponent :action="'edit'" :code="3" :id="sponsor.image_id" @Submission="handleImageStatusChange"/>
              <div style="width: 100%; display: flex; flex-direction: row; justify-content: center;">
                <v-btn @click="cancelImageChange()">CANCEL</v-btn>
              </div>
            </div>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
      <div style="margin-top: 12px">
        <v-btn @click="addSponsor()">ADD</v-btn>
      </div>
      <div v-if="add_sponsor" style="width: 100%; display: flex; flex-direction: column;">
        <!--No slash at the end of restful api for add.-->
        <ImageChangeComponent :action="'add'" :code="3" @Submission="handleImageStatusChange"/>
        <div style="width: 100%; display: flex; flex-direction: row; justify-content: center;">
          <v-btn @click="cancelImageChange()">CANCEL</v-btn>
        </div>
      </div>
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
      this.initializeFlyers(),
      this.initializeSponsors()
    },
    data() {
      return {
        add_carousel: false,
        edit_carousel: false,
        edit_flyer: false,
        add_sponsor: false,
        edit_sponsor: false,
        carousels: [],
        flyers: [],
        sponsors: []
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
      initializeSponsors() {
        apiClient.get("cms/sponsors").then(response => {
          this.sponsors = response.data.sponsors
          this.edit_sponsor = false
        })
      },
      // Binded with child component's this.$emit.
      // This val does not need to be defined manually.
      // `code` from child controls which initialize finction to call.
      handleImageStatusChange(payload) {
        if ( payload.code === 1 && payload.action === 'add' ) {
          apiClient.post('cms/carousels', {
            src: payload.src,
            link: payload.link
          }).then(response => {
            this.initializeCarousels()
          })
        } else if ( payload.code === 1 && payload.action === 'edit' ) {
          apiClient.put('cms/carousels/' + payload.id, {
            src: payload.src,
            link: payload.link
          }).then(response => {
            this.initializeCarousels()
          })
        } else if ( payload.code === 2 && payload.action === 'edit' ) {
          apiClient.put('cms/flyers/' + payload.id, {
            src: payload.src,
            link: payload.link
          }).then(response => {
            this.initializeFlyers()
          })
        } else if ( payload.code === 3 && payload.action === 'add' ) {
          apiClient.post('cms/sponsors', {
            src: payload.src,
            link: payload.link
          }).then(response => {
            this.initializeSponsors()
          })
        } else if ( payload.code === 3 && payload.action === 'edit' ) {
          apiClient.put('cms/sponsors/' + payload.id, {
            src: payload.src,
            link: payload.link
          }).then(response => {
            this.initializeSponsors()
          })
        }
      },
      addCarousel() {
        this.add_carousel = true
        this.edit_carousel = false
        this.edit_flyer = false
      },
      editCarousel(carousel) {
        this.add_carousel = false
        this.edit_carousel = true
        this.edit_flyer = false
        this.add_sponsor = false
        this.edit_sponsor = false
      },
      addSponsor() {
        this.add_sponsor = true
        this.edit_sponsor = false
      },
      editSponsor(carousel) {
        this.add_carousel = false
        this.edit_carousel = false
        this.add_sponsor = false
        this.edit_sponsor = true
        this.edit_flyer = false
      },
      deleteSponsor(sponsor_id) {
        apiClient.delete("cms/sponsors/" + sponsor_id, {
            src: this.sponsor_src,
            link: this.sponsor_link
        }).then(response => {
            console.log(response);
            this.initializeSponsors();
        }).catch(error => {
            console.log(error);
            // 请求失败时，你可能想要做一些事情
        });
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
        this.add_sponsor = false
        this.edit_sponsor = false
      }
    }
  }
</script>
