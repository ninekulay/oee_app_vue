<template>
  <div>
      <QDashboard :width="sidebar.width">
      <template #sider>
          <QSidebar v-model:is-collapsed="sidebar.isCollapsed" :width="sidebar.width" :is-television="true" @check-request="handleTvMode"/>
      </template>
      <template #content>
          <div class="flex flex-col justify-center overscroll-x-auto"></div>
          <div class="flex justify-center gap-4 w-full min-h-60 px-4 sm:flex-col lg:flex-row" :style="`${userScreenType === 'tablet' ? 'height: fit-content;' : 'height: calc(100vh - 15vh);'}`">
            <div class="sm:w-full lg:w-1/2 flex flex-col h-full relative bg-white my-2 shadow-xl rounded-xl border border-gray-100" :style="`${userScreenType !== 'tablet' ? 'min-height: 710px;': ''}`">
              <div class="h-full relative flex items-center justify-center">
                <!-- Image Container -->
                <div class="relative" :style="userScreenType === 'tablet' ? 'max-width: 500px; max-height: 500px;' : ''">
                  <img :src="robotPalletIcon" class="object-cover w-full" alt="robot pallet">

                  <div class="w-full px-4 flex flex-col items-center gap-2">
                    <div class="gap-4 bg-orange-200 border border-black flex justify-center py-2 rounded-md" style="width: 90%;">
                      <h2
                      class="font-semibold"
                      >
                        Machine Name : <span class="font-normal">{{ dataSource.friendlyMachineName }}</span>
                      </h2>
                      <h2
                      class="font-semibold"
                      >
                        SKU : <span class="font-normal">{{ dataSource.standardData.productionSetting.sku }}</span>
                      </h2>
                    </div>
                    <div :class="`gap-4 border border-black flex justify-center py-2 rounded-md ${backgroundMachineStatus}`" style="width: 90%;">
                      <h2
                      class="font-semibold"
                      >
                        Machine Status : <span class="font-normal">{{ dataSource.machineStatus }}</span>
                      </h2>
                    </div>
                  </div>

                  <button class="absolute" style="top: 1%; left: 4%;" @click="changeStateCard('positionProductionReport')">
                    <img :src="iconInfoCircle" class="object-cover" alt="search"/>
                  </button>

                  <button class="absolute" style="bottom: 15%; right: 4%;" @click="changeStateCard('positionProductionTime')">
                    <img :src="iconInfoCircle" class="object-cover" alt="search"/>
                  </button>
                  
                  <!-- <button class="absolute" style="top: 42%; left: 2%;" @click="changeStateCard('position1')">
                    <img :src="iconInfoCircle" class="object-cover" alt="search"/>
                  </button>

                  <button class="absolute" style="top: 15%; right: 2%;" @click="changeStateCard('position2')">
                    <img :src="iconInfoCircle" class="object-cover" alt="search"/>
                  </button> -->
                </div>
              </div>

              <div class="bg-gray-300 min-h-20 p-4 rounded-md flex flex-col gap-2 absolute shadow-lg" style="top: 2%; left: 4%;" v-if="displayCard.positionProductionReport">
                <div class="flex justify-between gap-4 items-center">
                  <div class="flex justify-start gap-4 items-center">
                    <!-- <div class="bg-green-400 rounded-full h-4 w-4 border border-black"></div> -->
                    <h2 class="text-black font-semibold">Production Report</h2>
                  </div>
                  <h2 class="text-black font-semibold border border-black rounded-full px-2 py-1 cursor-pointer bg-gray-100" @click="() => displayCard.positionProductionReport = false">X</h2>
                </div>
                <div class="bg-white h-full rounded-lg flex flex-col gap-2 py-2">
                  <div class="flex flex-col justify-start items-start px-4 gap-4">
                    <div class="grid grid-cols-[100px,auto,auto]">
                      <h2 class="text-left">Target</h2>
                      <h2 class="text-left">: {{dataSource.standardData.productionSetting.target}} pcs</h2>
                      <h2 class="text-left">/ {{ dataSource.standardData.productionSetting.target / dataSource.standardData.productionSetting.pcsPerLot }} Lot</h2>
                    </div>
                    <div class="grid grid-cols-[100px,auto,auto]">
                      <h2 class="text-left">Actual</h2>
                      <h2 class="text-left">: {{ dataSource.countItem }} pcs</h2>
                      <h2 class="text-left">/ {{ dataSource.countItem / dataSource.standardData.productionSetting.pcsPerLot }} Lot</h2>
                    </div>
                    <div class="grid grid-cols-[100px,auto,auto]">
                      <h2 class="text-left">Remain</h2>
                      <h2 class="text-left">: {{ dataSource.standardData.productionSetting.target - dataSource.countItem < 1 ? 0 : dataSource.standardData.productionSetting.target - dataSource.countItem}} pcs</h2>
                      <h2 class="text-left">/ {{ dataSource.standardData.productionSetting.target - dataSource.countItem < 0 ? 0 : (dataSource.standardData.productionSetting.target - dataSource.countItem) / dataSource.standardData.productionSetting.pcsPerLot }} Lot</h2>
                    </div>
                  </div>
                </div>
              </div>

              <div class="bg-gray-300 min-h-20 p-4 rounded-md flex flex-col gap-2 absolute shadow-lg" style="bottom: 20%; right: 4%;" v-if="displayCard.positionProductionTime">
                <div class="flex justify-between gap-4 items-center">
                  <div class="flex justify-start gap-4 items-center">
                    <!-- <div class="bg-green-400 rounded-full h-4 w-4 border border-black"></div> -->
                    <h2 class="text-black font-semibold">Production Time</h2>
                  </div>
                  <h2 class="text-black font-semibold border border-black rounded-full px-2 py-1 cursor-pointer bg-gray-100" @click="() => displayCard.positionProductionTime = false">X</h2>
                </div>
                <div class="bg-white h-full rounded-lg flex flex-col gap-2 py-2">
                  <div class="flex flex-col justify-start items-start px-4 gap-4 w-full">
                    <div class="grid grid-cols-[180px,150px]">
                      <h2 class="text-left">Estimate Time</h2>
                      <h2 class="text-left">: {{ (dataSource.standardData.productionSetting.target / dataSource.standardData.productionSetting.standardTime).toFixed(2) }} min.</h2>
                    </div>
                    <div class="grid grid-cols-[180px,150px]">
                      <h2 class="text-left">Production Time</h2>
                      <h2 class="text-left">: {{ dataSource.actualCycleTime > 0 ?  (dataSource.actualCycleTime / dataSource.standardData.productionSetting.standardTime).toFixed(2) : '-' }} min.</h2>
                    </div>
                    <div class="grid grid-cols-[180px,150px]">
                      <h2 class="text-left">Remain Time</h2>
                      <h2 class="text-left">: {{ dataSource.standardData.productionSetting.target - dataSource.countItem < 1 ? 0 : ((dataSource.standardData.productionSetting.target - dataSource.countItem)/dataSource.standardData.productionSetting.standardTime ).toFixed(2) }} min.</h2>
                    </div>
                  </div>
                </div>
              </div>

              <div class="bg-gray-300 min-h-20 p-4 rounded-md flex flex-col gap-2 absolute shadow-lg top-60" v-if="displayCard.position1">
                <div class="flex justify-between gap-4 items-center">
                  <div class="flex justify-start gap-4 items-center">
                    <div class="bg-green-400 rounded-full h-4 w-4 border border-black"></div>
                    <h2 class="text-black font-semibold">{{ dataSource.friendlyMachineName }}</h2>
                  </div>
                  <h2 class="text-black font-semibold border border-black rounded-full px-2 py-1 cursor-pointer bg-gray-100" @click="() => displayCard.position1 = false">X</h2>
                </div>
                <div class="bg-white h-full rounded-lg flex flex-col gap-2 py-2">
                  <h2>State : Berserk</h2>
                  <h2>Position : </h2>
                  <div class="flex justify-center gap-4">
                    <h2>X : 99</h2>
                    <h2>Y : 99</h2>
                    <h2>Z : 99</h2>
                  </div>
                </div>
              </div>

              <div class="bg-gray-300 min-h-20 p-4 rounded-md flex flex-col gap-2 absolute shadow-lg right-0 top-24" v-if="displayCard.position2">
                <div class="flex justify-between gap-4 items-center">
                  <div class="flex justify-start gap-4 items-center">
                    <div class="bg-green-400 rounded-full h-4 w-4 border border-black"></div>
                    <h2 class="text-black font-semibold">Storage</h2>
                  </div>
                  <h2 class="text-black font-semibold border border-black rounded-full px-2 py-1 cursor-pointer bg-gray-100" @click="() => displayCard.position2 = false">X</h2>
                </div>
                <div class="bg-white h-full rounded-lg flex flex-col gap-2 min-h-12 items-center justify-center">
                  <h2 class="px-4">Package count : 9,999</h2>
                </div>
              </div>
            </div>
            <div class="sm:w-full lg:w-1/2 flex flex-col h-full gap-4 my-2">
              <div class="w-full min-h-20 bg-white flex flex-col shadow-xl rounded-xl border border-gray-100" style="height: calc(100%); min-height: 230px;">
                <div class=" w-full flex justify-start items-center gap-2">
                  <h2 class="font-semibold bg-gray-300  px-2 py-1 mt-2 ml-2 rounded-md">OEE</h2>
                  <label class="text-gray-500 py-2 mt-2 text-sm">{{ this.dataSource.standardData.productionTime.timeFrom }} - {{ this.dataSource.standardData.productionTime.timeTo }}</label>
                </div>
                <div class="w-full h-full flex justify-center" v-show="userScreenType !== 'tablet2'">
                  <div class="w-full flex flex-col justify-center m-0 p-0 relative items-center">
                    <d-chart-circle-gauge :data-source="dataSource.oeeValue"/>
                    <label 
                        style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -100%); width: 100px;" 
                        class="text-black text-xl font-normal">
                        <span class="text-gray-500 sm:text-xs lg:text-md">{{ dataSource.oeeValue.data[0].y.toFixed(2) }}%</span>
                    </label>
                    <label class="font-semibold sm:text-sm lg:text-md">
                      Overall
                    </label>
                  </div>
                  <div class="w-full flex flex-col justify-center m-0 p-0 relative items-center">
                    <d-chart-circle-gauge :data-source="dataSource.aValue"/>
                    <label 
                        style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -100%); width: 100px;" 
                        class="text-black text-xl font-normal">
                        <span class="text-gray-500 sm:text-xs lg:text-md">{{ dataSource.aValue.data[0].y.toFixed(2) }}%</span>
                    </label>
                    <label class="font-semibold sm:text-sm lg:text-md">
                      Avaliability
                    </label>
                  </div>
                  <div class="w-full flex flex-col justify-center m-0 p-0 relative items-center">
                    <d-chart-circle-gauge :data-source="dataSource.pValue"/>
                    <label 
                        style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -100%); width: 100px;" 
                        class="text-black text-xl font-normal">
                        <span class="text-gray-500 sm:text-xs lg:text-md">{{ dataSource.pValue.data[0].y.toFixed(2) }}%</span>
                    </label>
                    <label class="font-semibold sm:text-sm lg:text-md">
                      Performance
                    </label>
                  </div>
                  <div class="w-full flex flex-col justify-center m-0 p-0 relative items-center">
                    <d-chart-circle-gauge :data-source="dataSource.qValue"/>
                    <label 
                        style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -100%); width: 100px;" 
                        class="text-black text-xl font-normal">
                        <span class="text-gray-500 sm:text-xs lg:text-md">{{ dataSource.qValue.data[0].y >= 100 ? 100 : dataSource.qValue.data[0].y.toFixed(2) }}%</span>
                    </label>
                    <label class="font-semibold sm:text-sm lg:text-md">
                      Quality
                    </label>
                  </div>
                </div>

                <div class="w-full h-full flex justify-center" v-show="userScreenType === 'tablet2'">
                  <div class="w-full flex flex-col justify-center">
                    <div class="flex justify-between items-center">
                      <div class="w-full flex flex-col justify-center m-0 p-0 relative items-center">
                        <d-chart-circle-gauge :data-source="dataSource.oeeValue"/>
                        <label 
                            style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -100%); width: 100px;" 
                            class="text-black text-xl font-normal">
                            <span class="text-gray-500 sm:text-xs lg:text-md">99.99%</span>
                        </label>
                        <label class="font-semibold sm:text-sm lg:text-md">
                          Overall
                        </label>
                      </div>
                      <div class="w-full flex flex-col justify-center m-0 p-0 relative items-center">
                        <d-chart-circle-gauge :data-source="dataSource.aValue"/>
                        <label 
                            style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -100%); width: 100px;" 
                            class="text-black text-xl font-normal">
                            <span class="text-gray-500 sm:text-xs lg:text-md">99.99%</span>
                        </label>
                        <label class="font-semibold sm:text-sm lg:text-md">
                          Avaliability
                        </label>
                      </div>
                    </div>
                    <div class="flex justify-between items-center">
                      <div class="w-full flex flex-col justify-center m-0 p-0 relative items-center">
                        <d-chart-circle-gauge :data-source="dataSource.pValue"/>
                        <label 
                            style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -100%); width: 100px;" 
                            class="text-black text-xl font-normal">
                            <span class="text-gray-500 sm:text-xs lg:text-md">99.99%</span>
                        </label>
                        <label class="font-semibold sm:text-sm lg:text-md">
                          Performance
                        </label>
                      </div>
                      <div class="w-full flex flex-col justify-center m-0 p-0 relative items-center">
                        <d-chart-circle-gauge :data-source="dataSource.qValue"/>
                        <label 
                            style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -100%); width: 100px;" 
                            class="text-black text-xl font-normal">
                            <span class="text-gray-500 sm:text-xs lg:text-md">99.99%</span>
                        </label>
                        <label class="font-semibold sm:text-sm lg:text-md">
                          Quality
                        </label>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
              <div class="w-full min-h-20 bg-white flex flex-col shadow-xl rounded-xl border border-gray-100 gap-4 utilization-div">
                <div class="w-full flex justify-start items-center gap-2">
                  <h2 class="font-semibold px-2 py-2 bg-gray-300 mt-2 ml-2 rounded-md">Utilization</h2>
                  <label class="text-gray-500 py-2 mt-2 text-sm">{{ dataSource.standardData.productionTime.timeFrom }} - {{ dataSource.standardData.productionTime.timeTo }}</label>
                </div>
                <div class="w-full px-4 flex sm:flex-col xl:flex-row justify-center h-full" style="margin-top: -15px;">
                    <div class="sm:w-full xl:w-2/3 h-full">
                      <d-chart-donut :data-source="dataSource.utilizationValue" style="height: 150px;"/>
                    </div>
                    <div class="sm:w-full xl:w-1/3 flex flex-col gap-4 items-center justify-center h-full progress-bar-div">
                      <span class="font-semibold">Production Progress</span>
                      <div class="w-full h-8 border border-gray-500 rounded-lg bg-white items-center flex">
                        <!-- Progress bar, width dynamically set -->
                        <div class="bg-black h-full rounded-lg" :style="{ width: dataSource.productionProgress + '%', height: 80 + '%', marginLeft: 1 + 'px', marginRight: 1 + 'px' }"></div>
                      </div>
                      <h2>{{ dataSource.productionProgress }}% <span class="text-sm text-gray-500">({{dataSource.countItem}} / {{dataSource.standardData.productionSetting.target}})</span></h2>
                    </div>
                </div>
              </div>
              <div class="w-full min-h-20 bg-white shadow-xl rounded-xl border border-gray-100" style="height: calc(75%); min-height: 220px;">
                <div class="w-full flex justify-start items-center gap-2">
                  <h2 class="font-semibold px-2 py-2 bg-gray-300 mt-2 ml-2 rounded-md">Timeline</h2>
                  <label class="text-gray-500 py-2 mt-2 text-sm">{{ dataSource.standardData.productionTime.timeFrom }} - {{ dataSource.standardData.productionTime.timeTo }}</label>
                </div>
                <div class="w-full px-4">
                  <d-chart-timeline :data-source="dataSource.timelineValue" style="height: 150px;"/>
                </div>
              </div>
            </div>
          </div>
      </template>
      </QDashboard>
  </div>
</template>

<script>
import QDashboard from '@/layouts/dashboard/q-dashboard-default.vue'
import QSidebar from '@/components/bar/sidebar.vue'
import { reactive, watch, ref } from 'vue'
import { iconInfoCircle, robotPalletIcon } from '@/utils/helper-asset-icon.ts'
import { DChartCircleGauge, DChartDonut, DChartTimeline } from '@/components/export'
import { getDataFromMachine } from '@/store/machineStatus'
import { getStatusLogsFromMachine } from '@/store/machineStatusLogs'
import { getStandardDataFromUser } from '@/store/standardSettings'
import { getUserAuth } from '@/store/userManagement'

export default {
  name: 'OverviewPage',
  components: { QSidebar, QDashboard, DChartCircleGauge, DChartDonut, DChartTimeline },
  setup () {
    const sidebar = reactive({
      size: {
        collapsed: 80,
        normal: 240
      },
      width: 80,
      isCollapsed: true
    })

    const userScreenType = ref('desktop')

    const displayCard = reactive({
      position1: false,
      position2: false,
      positionProductionReport: false,
      positionProductionTime: false
    })

    const isTelevisionMode = ref(false)

    watch(() => sidebar.isCollapsed, () => {
      sidebar.width = sidebar.isCollapsed ? sidebar.size.collapsed : sidebar.size.normal
    })

    const dataSource = reactive({
      friendlyMachineName: '',
      machineStatus: '',
      countItem: 0,
      actualCycleTime: 0,
      standardData: {
        productionTime: {
          timeFrom: '',
          timeTo: '',
        },
        productionSetting: {
          sku: '',
          target: 1,
          standardTime: 1,
          pcsPerLot: 1
        }
      },
      productionProgress: 0,
      oeeValue: {
        title: '',
        fontSize: '16px',
        chartWidth: '150',
        chartHeight: '150',
        data: [{
          color: '#0EA5E9',
          radius: '110%',
          innerRadius: '85%',
          y: 0
        }]
      },
      aValue: {
        title: '',
        fontSize: '16px',
        chartWidth: '150',
        chartHeight: '150',
        data: [{
          color: '#4fb891',
          radius: '110%',
          innerRadius: '85%',
          y: 0
        }]
      },
      pValue: {
        title: '',
        fontSize: '16px',
        chartWidth: '150',
        chartHeight: '150',
        data: [{
          color: '#f8d363',
          radius: '110%',
          innerRadius: '85%',
          y: 0
        }]
      },
      qValue: {
        title: '',
        fontSize: '16px',
        chartWidth: '150',
        chartHeight: '150',
        data: [{
          color: '#db8132',
          radius: '110%',
          innerRadius: '85%',
          y: 0
        }]
      },
      utilizationValue: {
        name: 'Production',
        data: [
          { name: 'Uptime', y: 0, color: '#6BE895' },
          { name: 'Downtime', y: 0, color: '#F27E6E' },
          { name: 'Idle', y: 0, color: '#FFE178' }
        ],
        unit: 'Production',
        width: 350,
        chartSize: 150,
        credits: false,
        legend: {
          textSize: 14,
        }
      },
      timelineValue: {
        name: '',
        data: []
      }
    })

    const parseDateToUTC = (dateStr) => {
      const [datePart, timePart] = dateStr.split(' ');
      const [year, month, day] = datePart.split('-').map(Number);
      const [hours, minutes, seconds] = timePart.split(':').map(Number);

      return Date.UTC(year, month - 1, day, hours, minutes, seconds); 
    }

    // const generateMockData = () => {
    //   let lastValue = ''
    //   const data = [];
    //   const startDate = new Date(Date.UTC(2024, 9, 19, 2, 0)); // Start from 00:00
    //   const endDate = new Date(Date.UTC(2024, 9, 20, 22, 0)); // Up to 22:00
    //   const totalEntries = 5; // Total number of data objects

    //   for (let i = 0; i < totalEntries; i++) {
    //     const randomStartHour = Math.floor(Math.random() * 2); // Random hour from 0 to 21
    //     const randomStartMinute = Math.floor(Math.random() * 60); // Random minute from 0 to 59
    //     const startTime = new Date(startDate);
    //     startTime.setHours(randomStartHour);
    //     startTime.setMinutes(randomStartMinute);

    //     // Generate a random duration between 30 minutes and 1.5 hours
    //     const durationInMinutes = Math.floor(Math.random() * (90 - 30 + 1)) + 30; // Random duration from 30 to 90 minutes
    //     const endTime = new Date(startTime);
    //     endTime.setMinutes(startTime.getMinutes() + durationInMinutes);

    //     // Ensure the end time does not exceed 22:00
    //     if (endTime > endDate) {
    //         continue; // Skip this entry if it exceeds the allowed time
    //     }

    //     // Determine the color and label based on random choice
    //     let isRunning = Math.random() > 0.5; // 50% chance to be "RUNNING" or "STOP"
    //     if (lastValue === 'RUNNING') {
    //       isRunning = false
    //     }
    //     const color = isRunning ? '#6BE895' : '#F27E6E';
    //     const label = isRunning ? 'RUNNING' : 'STOP';
    //     lastValue = label

    //     data.push({
    //         x: startTime.getTime(), // Start time (timestamp)
    //         x2: endTime.getTime(), // Stop time (timestamp)
    //         y: 0, // Y-axis category (Status)
    //         color: color, // Color for this state
    //         label: label // State label
    //     });
    //   }

    //   return data;
    // }

    // const mockData = generateMockData();
    // console.log('mockData', mockData);
    // dataSource.timelineValue.data = mockData;
    const checkScreenSize = () => {
      const screenWidth = window.innerWidth // Get the current screen width

      // Check screen width and apply the conditions
      if (screenWidth <= 768) {
        dataSource.oeeValue.chartWidth = '100'
        dataSource.oeeValue.chartHeight = '100'
        dataSource.aValue.chartWidth = '100'
        dataSource.aValue.chartHeight = '100'
        dataSource.pValue.chartWidth = '100'
        dataSource.pValue.chartHeight = '100'
        dataSource.qValue.chartWidth = '100'
        dataSource.qValue.chartHeight = '100'
        dataSource.utilizationValue.chartSize = 120
        dataSource.utilizationValue.legend.textSize = 10
        userScreenType.value = 'tablet'
      } else if (screenWidth <= 1024) {
        dataSource.oeeValue.chartWidth = '100'
        dataSource.oeeValue.chartHeight = '100'
        dataSource.aValue.chartWidth = '100'
        dataSource.aValue.chartHeight = '100'
        dataSource.pValue.chartWidth = '100'
        dataSource.pValue.chartHeight = '100'
        dataSource.qValue.chartWidth = '100'
        dataSource.qValue.chartHeight = '100'
        dataSource.utilizationValue.chartSize = 100
        dataSource.utilizationValue.legend.textSize = 12
        userScreenType.value = 'labtop'
      } else {
        dataSource.oeeValue.chartWidth = '150'
        dataSource.oeeValue.chartHeight = '150'
        dataSource.aValue.chartWidth = '150'
        dataSource.aValue.chartHeight = '150'
        dataSource.pValue.chartWidth = '150'
        dataSource.pValue.chartHeight = '150'
        dataSource.qValue.chartWidth = '150'
        dataSource.qValue.chartHeight = '150'
        dataSource.utilizationValue.chartSize = 150
        dataSource.utilizationValue.legend.textSize = 14
        userScreenType.value = 'desktop'
      }
    }
    checkScreenSize()
    return {
      sidebar,
      iconInfoCircle,
      robotPalletIcon,
      displayCard,
      isTelevisionMode,
      dataSource,
      parseDateToUTC,
      userScreenType
    }
  },
  computed: {
    backgroundMachineStatus () {
      return this.dataSource.machineStatus.toUpperCase() === 'RUN' ? 'bg-green-400' : this.dataSource.machineStatus.toUpperCase() === 'STOP' ? 'bg-red-400' : 'bg-yellow-400'
    }
    // computedWidth () {
    //   const screenWidth = window.innerWidth // Get the current screen width
    //   const maxWidth = 150 // Set the maximum width

    //   // Check screen width and apply the conditions
    //   if (screenWidth <= 1024) {
    //     return `${maxWidth}px` // Set to 200px if width > 200
    //   }
    //   return '325px' // Otherwise, return the original width
    // }
  },
  mounted () {
    const standardData = sessionStorage.getItem('standardData')
    const machineList = sessionStorage.getItem('machineList')
    if (standardData && machineList) {
      this.assignStandardData(JSON.parse(standardData))
      this.getCurrentMachineData()
    } else {
      this.getStdData()
    }
  },
  methods: {
    changeStateCard (position) {
      this.displayCard[position] = !this.displayCard[position]
    },
    handleTvMode () {
      this.isTelevisionMode = !this.isTelevisionMode
      if (this.isTelevisionMode) {
        this.sidebar.isCollapsed = true
      }
    },
    convertSecondToMinute (second) {
      const minutes = Math.floor(second / 60); // Full minutes
      const seconds = second % 60;
      return parseFloat(`${minutes}.${seconds}`);
    },
    assignValueToUtilizationChart (data) {
      const upTime = data.operate_data.run_time > 0 ? this.convertSecondToMinute(data.operate_data.run_time) : 0
      const downTime = data.operate_data.stop_time > 0 ? this.convertSecondToMinute(data.operate_data.stop_time) : 0
      const idleTime = data.operate_data.idle_time > 0 ? this.convertSecondToMinute(data.operate_data.idle_time) : 0
      this.dataSource.utilizationValue.data[0] = upTime
      this.dataSource.utilizationValue.data[1] = downTime
      this.dataSource.utilizationValue.data[2] = idleTime
    },
    assignValueToOeeChart (data) {
      this.dataSource.oeeValue.data[0].y = data.operate_data.oee_value
      this.dataSource.aValue.data[0].y = data.operate_data.a_value
      this.dataSource.pValue.data[0].y = data.operate_data.p_value
      this.dataSource.qValue.data[0].y = data.operate_data.q_value
    },
    async getCurrentMachineData () {
      const sendParams = {
        machine_name: 'ajinomoto-mc_1',
        line_name: 'ajinomoto_pathum',
        location: 'ajinomoto',
      }
      const data = await getDataFromMachine(sendParams)
      if (data.length > 0) {
        const obj = data[0]
        this.assignValueToUtilizationChart(obj)
        this.assignValueToOeeChart(obj)
        this.assugnStandardDataDisplay(obj)
      }
      this.getStatusLogs()
    },
    assugnStandardDataDisplay (data) {
      this.dataSource.machineStatus = data.machine_status.toUpperCase()
      this.dataSource.friendlyMachineName = data.friendly_machine_name
      this.dataSource.standardData.productionSetting.sku = data.production_setting.product_id
      this.dataSource.standardData.productionSetting.target = data.production_setting.production_target
      this.dataSource.standardData.productionSetting.standardTime = data.production_setting.standard_time
      this.dataSource.standardData.productionSetting.pcsPerLot = data.production_setting.pcs_per_lot
      this.dataSource.countItem = data.operate_data.count_item
      let progessCount = (data.operate_data.count_item / data.production_setting.production_target) * 100
      progessCount = parseFloat(progessCount) > 100 ? 100 : progessCount
      this.dataSource.productionProgress = !isNaN(progessCount) && progessCount !== undefined && progessCount !== null ? progessCount.toFixed(2) : 0
    },
    convertToDateUTC (dateStr) {
      const [datePart, timePart] = dateStr.split(' ');
      const [year, month, day] = datePart.split('-').map(Number);
      const [hours, minutes, seconds] = timePart.split(':').map(Number);

      return Date.UTC(year, month - 1, day, hours, minutes, seconds); 
    },
    assignValueToTimelineChart (data) {
      this.dataSource.timelineValue.data = []
      let newData = []
      data.forEach((element, index) => {
        if (index < data.length - 1) {
          let machineStatus = element.status
          let color = '';
          let label = '';
          switch (machineStatus) {
            case 'run':
              color = '#6BE895'
              label = 'RUNNING'
              break;
            case 'stop':
              color = '#F27E6E'
              label = 'STOP'
              break;
            case 'idle':
              color = '#f8d363'
              label = 'IDLE'
              break;
          }

          newData.push({
              x:  this.convertToDateUTC(element.created_at), // Start time (timestamp)
              x2: this.convertToDateUTC(data[index + 1].created_at), // Stop time (timestamp)
              y: 0, // Y-axis category (Status)
              color: color, // Color for this state
              label: label // State label
          });
        }
      })
      this.dataSource.timelineValue.data = [...newData]
    },
    async getStatusLogs () {
      const now = new Date()
      // const formattedDate = `${now.getFullYear()}-${(now.getMonth() + 1).toString().padStart(2, '0')}-${now.getDate().toString().padStart(2, '0')} ${now.getHours().toString().padStart(2, '0')}:${now.getMinutes().toString().padStart(2, '0')}:${now.getSeconds().toString().padStart(2, '0')}`
      const formattedDate = `${now.getFullYear()}-${(now.getMonth() + 1).toString().padStart(2, '0')}-${now.getDate().toString().padStart(2, '0')}`
      const tomorrow = new Date()
      tomorrow.setDate(tomorrow.getDate() + 1)
      const formattedTomorrow = `${tomorrow.getFullYear()}-${(tomorrow.getMonth() + 1).toString().padStart(2, '0')}-${tomorrow.getDate().toString().padStart(2, '0')}`

      const sendParams = {
        machine_name: 'ajinomoto-mc_1',
        line_name: 'ajinomoto_pathum',
        location: 'ajinomoto',
        time_from: `${formattedDate} ${this.dataSource.standardData.productionTime.timeFrom}:00`,
        time_to: `${formattedTomorrow} ${this.dataSource.standardData.productionTime.timeTo}:00`
      }
      const data = await getStatusLogsFromMachine(sendParams)
      if (data.length > 0) {
        this.assignValueToTimelineChart(data)
      }
    },
    async getStdData () {
      const userAuth = await getUserAuth()
      if (userAuth) {
        const data = await getStandardDataFromUser({ email: userAuth.username })
        if (data.length > 0) {
          const groupDataByLine = {}
          data.forEach(item => {
            if (!Object.prototype.hasOwnProperty.call(groupDataByLine, item.line_name)) {
              groupDataByLine[item.line_name] = {}
              groupDataByLine[item.line_name][`${item.data_type}`] = []
              groupDataByLine[item.line_name][`${item.data_type}`].push(item)
            } else {
              if (!Object.prototype.hasOwnProperty.call(groupDataByLine[item.line_name], `${item.data_type}`)) {
                groupDataByLine[item.line_name] = {}
                groupDataByLine[item.line_name][`${item.data_type}`] = []
              }
              groupDataByLine[item.line_name][`${item.data_type}`].push(item)
            }
          })
          this.assignStandardData(groupDataByLine['ajinomoto_pathum'])
          sessionStorage.setItem('standardData', JSON.stringify(groupDataByLine['ajinomoto_pathum']))
          sessionStorage.setItem('lineList', JSON.stringify(Object.keys(groupDataByLine)))
          this.getCurrentMachineData()
        }
      }
    },
    assignStandardData (data) {
      for (const key in data) {
        if (data[key] !== null && data[key] !== undefined) {
          switch (key) {
            case 'production_time':
              this.dataSource.standardData.productionTime.timeFrom = data[key][0].setting_data.time_from
              this.dataSource.standardData.productionTime.timeTo = data[key][0].setting_data.time_to
              break;
          }
        }
      }
    }
  }
}
</script>
<style scoped>
.utilization-div {
  height: calc(75%);
  min-height: 230px;
}
.progress-bar-div {
  margin-top: -15px;
}
@media (max-width: 1280px) {
  .utilization-div {
    min-height: 330px;
  }
  .progress-bar-div {
    margin-top: 0px;
  }
}
@media (min-width: 1280px) {
  .utilization-div {
    min-height: 230px;
  }
}
</style>
