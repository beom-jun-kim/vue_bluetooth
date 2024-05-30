<template>
  <button class="btn" @click="requestBluetoothDevice()">
    블루투스 연결하기
  </button>
</template>

<script>
import { ref } from 'vue'
import { pausableWatch, useBluetooth } from '@vueuse/core'

export default {
  name: 'BluetoothCom',
  props: {
    msg: String
  },
  setup() {
    const { isConnected, server } = useBluetooth({
      acceptAllDevices: true,
      optionalServices: ['battery_service'],
    })
    const batteryPercent = ref()
    const isGettingBatteryLevels = ref(false)

    async function getBatteryLevels() {
      isGettingBatteryLevels.value = true
      // Get the battery service:
      const batteryService = await server.getPrimaryService('battery_service')
      // Get the current battery level
      const batteryLevelCharacteristic = await batteryService.getCharacteristic('battery_level')
      // Listen to when characteristic value changes on `characteristicvaluechanged` event:
      batteryLevelCharacteristic.addEventListener('characteristicvaluechanged', (event) => {
        batteryPercent.value = event.target.value.getUint8(0)
      })
      // Convert received buffer to number:
      const batteryLevel = await batteryLevelCharacteristic.readValue()
      batteryPercent.value = await batteryLevel.getUint8(0)
    }

    const { stop } = pausableWatch(isConnected, (newIsConnected) => {
      if (!newIsConnected || !server.value || isGettingBatteryLevels.value) return
      // Attempt to get the battery levels of the device:
      getBatteryLevels()
      // We only want to run this on the initial connection, as we will use an event listener to handle updates:
      stop()
    })

    async function requestBluetoothDevice() {
      try {
        // 요청하기 전에 Bluetooth 지원 여부를 확인합니다.
        if (!navigator.bluetooth) {
          throw new Error('브라우저가 Bluetooth를 지원하지 않습니다.')
        }

        // Bluetooth 장치를 요청합니다.
        await navigator.bluetooth.requestDevice({
          acceptAllDevices: true,
          optionalServices: ['battery_service'],
        })

        // 연결되었을 때 처리할 로직을 작성합니다.
        // ...

      } catch (error) {
        console.error('Bluetooth 장치를 요청하는 동안 에러가 발생했습니다:', error)
      }
    }

    return {
      requestBluetoothDevice
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.btn {
  padding: 10px 20px;
  background: none;
  border: 1px solid #c3c3c3;
  font-size: 30px;
  cursor: pointer;
}
</style>
