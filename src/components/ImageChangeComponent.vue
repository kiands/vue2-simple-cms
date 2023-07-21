<template>
  <div style="margin-top: 12px; display: flex; flex-direction: column; justify-content: center;">
    <div>IMAGE LINK</div>
    <v-text-field v-model="carousel_src"></v-text-field>
    <div>HYPERLINK</div>
    <v-text-field v-model="carousel_link"></v-text-field>
    <div style="margin-top: 12px; margin-bottom: 12px; display: flex; flex-direction: row; justify-content: center;">
      <v-btn style="margin-right: 24px" @click="previewSRC()">PREVIEW</v-btn>
      <!--`action` is a property from parent.-->
      <v-btn style="margin-right: 24px" @click="submitImageChange(action, api, code)">SUBMIT</v-btn>
    </div>
    <v-card v-if="preview_carousel">
      <v-img :src=carousel_src></v-img>
    </v-card>
  </div>
</template>

<script>
  import apiClient from "@/api.js"
  export default {
    name: 'ImageChangeComponent',
    props: ['action', 'api', 'code', 'id'],
    data() {
      return {
        preview_carousel: false,
        carousel_src: "",
        carousel_link: "",
      };
    },
    methods: {
      previewSRC() {
        this.preview_carousel = true;
      },
      submitImageChange(action, api, code) {
        if (action === "edit") {
          /*
          for(let i = 0; i < this.carousels.length; i++) {
            if(this.carousels[i].carousel_id === this.editing_carousel_id) {  //找到需要替换的carousel
              this.carousels[i] = {  //替换carousel
                carousel_id: this.editing_carousel_id,
                src: this.carousel_src,
                link: this.carousel_link
              }
              break
            }
          }
          */
          apiClient.put(api + this.id, {
            src: this.carousel_src,
            link: this.carousel_link
          }).then(response => {
            this.$emit('ImageStatusChange', code)
          }).catch(error => {
            console.log(error);
            // 请求失败时，你可能想要做一些事情
          });
        } else {
          apiClient.post(api, {
            src: this.carousel_src,
            link: this.carousel_link
          }).then(response => {
            this.$emit('ImageStatusChange', code)
          }).catch(error => {
            console.log(error);
            // 请求失败时，你可能想要做一些事情
          });
        }
      },
    }
  }
</script>