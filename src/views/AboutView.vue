<template>
  <v-container style="display: flex; flex-direction: column; align-items: center;  margin-top: 12px; margin-bottom: 12px">
    <!--Programs Management-->
    <v-row style="width: 80%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
      <div style="margin-bottom: 12px">Programs Management</div>
      <v-expansion-panels>
        <v-expansion-panel
          v-for="(program, i) in programs"
          :key=i
          @click="cancelChanges()"
        >
          <v-expansion-panel-header>
            {{ i + 1 }}
          </v-expansion-panel-header>
          <v-expansion-panel-content>
            <v-card>
              <v-img :src=program.source></v-img>
              <div>{{ program.link }}</div>
              <div>{{ program.title }}</div>
              <div>{{ program.content }}</div>
            </v-card>
            <div style="margin-top: 12px; display: flex; flex-direction: row; justify-content: center;">
              <v-btn style="margin-right: 24px" @click="editProgram(program)">EDIT</v-btn>
              <v-btn @click="deleteProgram(program.program_id)">DELETE</v-btn>
            </div>
            <div v-if="edit_program" style="width: 100%; display: flex; flex-direction: column;">
              <!--`action`: type of mutation; `api`: assign api dynamically; `code`: defines which group of images to change; `id`: only for edit by id, not for add.-->
              <!--图片编辑-->
              <ImageChangeComponent :action="'edit'" :code="1" :id="program.program_id" @Submission="handleSubmission"/>
              <!--内容编辑-->
              <div>TITLE</div>
              <v-text-field v-model="current_program.title"></v-text-field>
              <div>CONTENT</div>
              <v-text-field v-model="current_program.content"></v-text-field>
              <div style="width: 100%; display: flex; flex-direction: row; justify-content: center;">
                <v-btn @click="cancelChanges()">CANCEL</v-btn>
              </div>
            </div>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
      <div style="margin-top: 12px">
        <v-btn @click="addProgram()">ADD</v-btn>
      </div>
      <div v-if="add_program" style="width: 100%; display: flex; flex-direction: column;">
        <!--No slash at the end of restful api for add.-->
        <ImageChangeComponent :action="'add'" :code="1" @Submission="handleSubmission"/>
        <div>TITIE</div>
        <v-text-field v-model="current_program.title"></v-text-field>
        <div>CONTENT</div>
        <v-text-field v-model="current_program.content"></v-text-field>
        <div style="width: 100%; display: flex; flex-direction: row; justify-content: center;">
          <v-btn @click="cancelChanges()">CANCEL</v-btn>
        </div>
      </div>
    </v-row>
  </v-container>
</template>

<script>
  import apiClient from "@/api.js"
  import ImageChangeComponent from "@/components/ImageChangeComponent.vue"

  export default {
    name: "AboutView",
    components: { ImageChangeComponent },
    data() {
      return {
        add_program: false,
        edit_program: false,
        programs: [],
        current_program: { program_id: '', source: '', link: '', title: '', content: '' }
      };
    },
    mounted() {
      this.initializePrograms()
    },
    methods: {
      initializePrograms() {
        apiClient.get("cms/programs").then(response => {
          this.programs = response.data.programs
          this.edit_program = false
          this.add_program = false
        })
      },
      editProgram(program) {
        this.add_program = false
        this.edit_program = true
        this.current_program = program
      },
      addProgram() {
        this.add_program = true
        this.edit_program = false
        this.current_program = { program_id: '', source: '', link: '', title: '', content: '' }
      },
      cancelChanges() {
        this.add_program = false
        this.edit_program = false
        this.current_program = { program_id: '', source: '', link: '', title: '', content: '' }
      },
      handleSubmission(payload) {
        if ( payload.code === 1 && payload.action === 'add' ) {
          apiClient.post('cms/programs', {
            src: payload.src,
            link: payload.link,
            title: this.current_program.title,
            content: this.current_program.content
          }).then(response => {
            this.initializePrograms()
          })
        } else if ( payload.code === 1 && payload.action === 'edit' ) {
          apiClient.put('cms/programs/' + payload.id, {
            src: payload.src,
            link: payload.link,
            title: this.current_program.title,
            content: this.current_program.content
          }).then(response => {
            this.initializePrograms()
          })
        }
      },
      deleteProgram(program_id) {
        apiClient.delete("cms/programs/" + program_id).then(response => {
            console.log(response);
            this.initializePrograms();
        }).catch(error => {
            console.log(error);
            // 请求失败时，你可能想要做一些事情
        });
      }
    }
  }
</script>
