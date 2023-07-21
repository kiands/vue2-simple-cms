<template>
  <v-container style="margin-top: 12px; margin-bottom: 12px">
    <v-row style="display_flex; flex-direction: column; alin-items: center">
      <div
        v-for="service in services"
        :key="service.booking_id"
        style="display: flex; flex-direction: row; align-items: center; justify-content: space-between;"
      >
        <div style="display: grid; grid-template-columns: repeat(2, 1fr); align-items: center;">
          <div style="margin-right: 10px">{{ service.booking_id }}</div>
          <div>{{ service.status }}</div>
        </div>
        <v-card style="margin: 5px; padding: 5px; height: 50px; width: 90%; display: grid; grid-template-columns: repeat(5, 1fr); align-items: center;">
          <div style="display: flex; flex-direction: row; align-items: center;">{{ service.service_name }}</div>
          <div style="display: flex; flex-direction: row; align-items: center;">{{ service.date }} {{ service.time }}</div>
          <div style="display: flex; flex-direction: row; align-items: center;"><v-btn @click="showMessage(service.message)">SHOW MESSAGE</v-btn></div>
          <div style="display: flex; flex-direction: row; align-items: center;">{{ service.user_email }}</div>
          <div style="display: flex; flex-direction: row; align-items: center; justify-content: space-around;">
            <v-btn @click="revokeBooking(service)">REVOKE</v-btn>
            <v-btn @click="finishBooking(service)">FINISH</v-btn>
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
    name: 'ServicesManagement',
    data () {
      return {
        length: 1,
        page: 1,
        services: []
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
        apiClient.get('cms/services').then(response => {
          // console.log(response.data.count)
          this.length = Math.floor(response.data.count / 10) + 1
          this.services = response.data.services
        })
      },

      fetchContent(page) {
        // 这里是拉取内容的逻辑，你可以根据实际需求来实现，例如使用axios发送一个GET请求
        apiClient.get('your-api-url?page=' + page).then(response => {
          this.content = response.data
        })
      },
      revokeBooking(service) {
        apiClient.post('cms/revoke_booking', service).then(
          response => {
            this.initialize()
          }
        )
      }
    },
    mounted() {
      this.initialize()
    }
  }
</script>