<template>
  <v-container style="margin-top: 12px; margin-bottom: 12px">
    <v-row style="margin-bottom: 12px; display_flex; flex-direction: column; alin-items: center">
      <div>Services Booking Management</div>
      <div
        v-for="booked_service in booked_services"
        :key="booked_service.booking_id"
        style="display: flex; flex-direction: row; align-items: center; justify-content: space-between;"
      >
        <div style="width: 15%; display: grid; grid-template-columns: repeat(2, 1fr); align-items: center;">
          <div style="margin-right: 10px">{{ "ID:" }} {{ booked_service.booking_id }}</div>
          <div>{{ "Status:" }} {{ booked_service.status }}</div>
        </div>
        <v-card style="margin: 5px; padding: 5px; height: 50px; width: 85%; display: grid; grid-template-columns: repeat(5, 1fr); align-items: center;">
          <div style="display: flex; flex-direction: row; align-items: center;">{{ booked_service.service_name }}</div>
          <div style="display: flex; flex-direction: row; align-items: center;">{{ booked_service.date }} {{ booked_service.time }}</div>
          <div style="display: flex; flex-direction: row; align-items: center;">
            <v-btn @click="showMessage(booked_service.message)">SHOW MESSAGE</v-btn>
            <v-dialog v-model="dialog" max-width="500px"><v-card>{{ message }}</v-card></v-dialog>
          </div>
          <div style="display: flex; flex-direction: row; align-items: center;">{{ booked_service.user_email }}</div>
          <div style="display: flex; flex-direction: row; align-items: center; justify-content: space-around;">
            <v-btn @click="revokeBooking(booked_service)">REVOKE</v-btn>
            <v-btn @click="finishBooking(booked_service)">FINISH</v-btn>
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
    <v-row style="margin-bottom: 16px;">
      <div>Services Management</div>
      <v-expansion-panels>
        <v-expansion-panel
          v-for="(service, i) in services"
          :key=i
          @click="setCurrentChosenServiceIndex(i)"
        >
          <v-expansion-panel-header>
            {{ i + 1 + ':' }} {{ service['service_name'] }}
          </v-expansion-panel-header>
          <v-expansion-panel-content>
            <div style="display: flex; flex-direction: row; justify-content: space-around; align-items: center;">
              <div>
                <v-date-picker
                  v-model="currentChosenDate"
                  class="mt-4"
                  min="2023-01-01"
                  max="2030-01-01"
                ></v-date-picker>
              </div>
              <div style="width: 50%">
                <v-text-field
                  v-for="(time, i) in timeSlots"
                  :key=i
                  :label=time
                  v-model=counts[i]
                  :rules="rules"
                  hide-details="auto"
                ></v-text-field>
              </div>
            </div>
            <div style="margin-top: 16px; display: flex; flex-direction: row; justify-content: space-around; align-items: center;">
              <v-btn @click="updateService(service['service_name'])">UPDATE</v-btn>
              <v-btn @click="deleteService(service['service_id'])">DELETE</v-btn>
            </div>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
    </v-row>
    <v-row style="margin-bottom: 16px; display: flex; flex-direction: column">
      <div>Services Management</div>
      <div style="display: flex; flex-direction: row; justify-content: center;">
        <v-btn @click="addNewService()">ADD SERVICE</v-btn>
      </div>
      <div v-if="add_service" style="width: 100%; display: flex; flex-direction: column; align-items: center">
        <div style="margin-bottom: 16px; width: 100%">
          <v-text-field
            label="new service name"
            v-model=new_service_name
            hide-details="auto"
            style="width: 100%"
          ></v-text-field>
          <v-text-field
            label="new service description"
            v-model=new_service_description
            hide-details="auto"
            style="width: 100%"
          ></v-text-field>
        </div>
        <v-btn @click="submitNewService()">SUBMIT</v-btn>
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
        booked_services: [],
        services: [],
        add_service: false,
        new_service_name: '',
        new_service_description: '',
        currentChosenDate: '',
        currentChosenServiceIndex: '',
        timeSlots: [ "07:00", "08:00", "09:00", "10:00", "11:00", "12:00", "13:00", "14:00", "15:00", "16:00", "17:00", "18:00" ],
        counts: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
        rules: [
          v => (!v || !isNaN(parseFloat(v))) || 'Input must be a number or empty'
        ],
        dialog: false,
        message: ''
      }
    },
    watch: {
      // 监听page属性
      page: function(newPage, oldPage) {
        this.fetchContent(newPage) // 当页数发生变化时，调用fetchContent方法
      },
      currentChosenDate: function(newChosenDate, oldChosenDate) {
        // The response is an array, the contents of the array are objects but the contents of the objects include raw JSON.
        let counts = JSON.parse(this.services[this.currentChosenServiceIndex]['time_slots'])[newChosenDate]
        let temp_counts = []
        for (let i = 0; i < this.timeSlots.length; i++) {
          try {
            counts[this.timeSlots[i]]
            temp_counts.push(counts[this.timeSlots[i]])
          } catch (error) {
            temp_counts.push(0) 
          }
        }
        this.counts = temp_counts
      }
    },
    methods: {
      initializeBookedServices() {
        apiClient.get('cms/booked_services').then(response => {
          // console.log(response.data.count)
          this.length = Math.floor(response.data.count / 10) + 1
          this.booked_services = response.data.services
        })
      },
      initializeServices() {
        apiClient.get('cms/services').then(response => {
          this.services = response.data.services
        })
      },
      setCurrentChosenServiceIndex(service_index) {
        this.currentChosenServiceIndex = service_index
        console.log(this.currentChosenServiceIndex)
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
            this.initializeBookedServices()
          }
        )
      },
      addNewService() {
        this.add_service = true
      },
      submitNewService() {
        apiClient.post('cms/services', { service_name: this.new_service_name, service_description: this.new_service_description }).then(response => {
          this.add_service = false
          this.initializeServices()
        })
      },
      updateService(service_name) {
        let slots = {}
        for (let i = 0; i < this.timeSlots.length; i++) {
          slots[this.timeSlots[i]] = this.counts[i]
        }
        let payload = { [this.currentChosenDate]: slots }
        if (this.currentChosenDate !== "") {
          apiClient.put('cms/services', { service_name: service_name, date: this.currentChosenDate, new_time_slots: payload }).then(response => {
            // console.log(response.data)
            this.initializeServices()
          }) 
        } else {
          console.log(payload)
        }
      },
      deleteService(service_id) {
        apiClient.delete('cms/services/' + service_id ).then(response => {
          // console.log(response.data)
          this.initializeServices()
        }) 
      },
      showMessage(info) {
        this.dialog = true
        if (info === '' || info === null) {
          this.message = 'Nothing provided.'
        } else {
          this.message = info
        }
      }
    },
    mounted() {
      this.initializeBookedServices()
      this.initializeServices()
    }
  }
</script>