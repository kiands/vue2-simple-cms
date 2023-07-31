<template>
  <v-container style="margin-top: 12px; margin-bottom: 12px">
    <v-row style="display_flex; flex-direction: column; alin-items: center">
      <div>
        <v-card style="margin: 5px; padding: 3px; height: 40px; display: grid; grid-template-columns: repeat(6, 1fr); align-items: center;"
          v-for="contact in contacts"
          :key="contact.contact_id"
        >
          <div>{{ "ID:" }} {{ contact.contact_id }}</div>
          <div>{{ "Name:" }} {{ contact.name }}</div>
          <div>{{ "Email:" }} {{ contact.email }}</div>
          <div>{{ "Source:" }} {{ contact.source }}</div>
          <div>{{ "Reason:" }} {{ contact.reason }}</div>
          <div>
            {{ "Additional Information:" }} <v-btn @click="showAdditionalInformation(contact.additional_information)">SHOW</v-btn>
            <v-dialog v-model="dialog" max-width="500px"><v-card>{{ additional_information }}</v-card></v-dialog>
          </div>
        </v-card>
      </div>
      <div class="text-center">
        <v-pagination
          v-model="page"
          :length=this.length
          :total-visible="7"
        ></v-pagination>
      </div>
    </v-row>
  </v-container>
</template>

<script>
  import apiClient from "@/api.js"
  export default {
    name: 'ContactsManagement',
    data () {
      return {
        length: 1,
        page: 1,
        dialog: false,
        additional_information: '',
        contacts: []
      }
    },
    watch: {
      // 监听page属性
      page: function(newPage, oldPage) {
        this.fetchContent(newPage) // 当页数发生变化时，调用fetchContent方法
      },
    },
    methods: {
      initialize() {
        apiClient.get('cms/contacts').then(response => {
          // console.log(response.data.count)
          this.length = Math.floor(response.data.count / 10) + 1
          this.contacts = response.data.contacts
        })
      },
      fetchContent(page) {
        // 这里是拉取内容的逻辑，你可以根据实际需求来实现，例如使用axios发送一个GET请求
        apiClient.get('your-api-url?page=' + page).then(response => {
          this.content = response.data
        })
      },
      showAdditionalInformation(info) {
        this.dialog = true
        if (info === '' || info === null) {
          this.additional_information = 'Nothing provided.'
        } else {
          this.additional_information = info
        }
      }
    },
    mounted() {
      this.initialize()
    }
  }
</script>
