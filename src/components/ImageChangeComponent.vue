<template>
  <div style="margin-top: 12px; display: flex; flex-direction: column; justify-content: center;">
    <div>IMAGE LINK</div>
    <v-text-field v-model="src"></v-text-field>
    <div>HYPERLINK</div>
    <v-text-field v-model="link"></v-text-field>
    <div style="margin-top: 12px; margin-bottom: 12px; display: flex; flex-direction: row; justify-content: center;">
      <v-btn style="margin-right: 24px" @click="previewSRC()">PREVIEW</v-btn>
      <!--`action` is a property from parent.-->
      <v-btn style="margin-right: 24px" @click="submitChanges(action, code)">SUBMIT</v-btn>
    </div>
    <v-card v-if="preview_image">
      <v-img :src=src></v-img>
    </v-card>
  </div>
</template>

<script>
  export default {
    name: 'ImageChangeComponent',
    props: ['action', 'code', 'id'],
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
      submitChanges(action, code) {
        if (action === "edit") {
          this.$emit('Submission', { action: action, code: code, id: this.id, src: this.src, link: this.link })
        } else {
          this.$emit('Submission', { action: action, code: code, src: this.src, link: this.link })
        }
      },
    }
  }
</script>