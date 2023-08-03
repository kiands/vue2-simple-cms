<template>
  <div style="margin-top: 12px; display: flex; flex-direction: column; justify-content: center;">
    <div>IMAGE LINK</div>
    <v-text-field v-model="src"></v-text-field>
    <div>HYPERLINK</div>
    <v-text-field v-model="link"></v-text-field>
    <div style="margin-top: 12px; margin-bottom: 12px; display: flex; flex-direction: row; justify-content: center;">
      <v-btn style="margin-right: 24px" @click="previewSRC()">PREVIEW</v-btn>
      <!--`action` is a property from parent.-->
      <v-btn style="margin-right: 24px" @click="submitChanges(action, api, code)">SUBMIT</v-btn>
    </div>
    <v-card v-if="preview_image">
      <v-img :src=src></v-img>
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
        preview_image: false,
        src: "",
        link: "",
      };
    },
    methods: {
      previewSRC() {
        this.preview_image = true;
      },
      submitChanges(action, api, code) {
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
            src: this.src,
            link: this.link
          }).then(response => {
            this.$emit('Submission', code)
          }).catch(error => {
            console.log(error);
            // 请求失败时，你可能想要做一些事情
          });
        } else {
          apiClient.post(api, {
            src: this.src,
            link: this.link
          }).then(response => {
            this.$emit('Submission', code)
          }).catch(error => {
            console.log(error);
            // 请求失败时，你可能想要做一些事情
          });
        }
      },
    }
  }
</script>