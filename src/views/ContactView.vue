<template>
  <v-container style="margin-top: 12px; margin-bottom: 12px">
    <v-row style="display_flex; flex-direction: column; alin-items: center">
      <div
        v-for="contact in contacts"
        :key="contact.contact_id"
      >
        <v-card style="margin: 5px; padding: 3px; height: 40px; display: flex; flex-direction: row; justify-content: space-between; align-items: center;">
          <div>{{ contact.contact_id }}</div>
          <div>{{ contact.name }}</div>
          <div>{{ contact.email }}</div>
          <div>{{ contact.source }}</div>
          <div>{{ contact.reason }}</div>
          <div>{{ contact.additional_information }}</div>
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
    },
    mounted() {
      this.initialize()
    }
  }
</script>
