<template>
  <div id="Footer" class="prevent-select cursor-default">
    <div id="FloatingInfo" class="hidden">
      <div id="ServerDate" class="text-left">Release Date: {{ serverInfo?.ngenCerf_date }}</div>
    </div>
    <div class="grid grid-rows-1 gap-1">
      <div class="row-span-1 footerColor text-sm">
        <div id="FooterData" class="version">
          <span @mouseenter="showServerInfo" @mouseleave="hideServerInfo">Version:
            {{ serverInfo?.ngenCerf_version }}</span>
        </div>
        <div class="copyright" @click="testTimeout()">Copyright {{ serverInfo?.ngenCerf_copyright}}</div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { generalStore } from "@/stores/common/GeneralStore";
import { useUserDataStore } from "@/stores/common/UserDataStore";

import { useToast } from "primevue/usetoast";
import type { ToastMessageOptions } from "primevue/toast";
import { ToastTimeout } from "@/composables/NgencerfEnums";

const toast = useToast();

const { serverInfo } = storeToRefs(generalStore());
const { addToastRecord } = generalStore();
const { testServerTimeout } = useUserDataStore();

const showServerInfo = () => {
  const e = document.getElementById('FloatingInfo');
  (e as HTMLElement).style.display = "inline-block"
}

const hideServerInfo = () => {
  const e = document.getElementById('FloatingInfo');
  (e as HTMLElement).style.display = "none"
}

const testTimeout = async() => {
  await testServerTimeout(40).then(response => {
    if (response?.message) {
      const tMsg: ToastMessageOptions = { severity: 'info', summary: 'Timeout test successful', detail: response.message, life: ToastTimeout.timeoutInfo };
      toast.add(tMsg); addToastRecord(tMsg);
    } else {
      const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Timeout test unsuccessful', detail: 'Server timed out before it could return a response.', life: ToastTimeout.timeoutError };
      toast.add(tMsg); addToastRecord(tMsg);
    }
  });
}


</script>

<style lang="scss" scoped>
@use '@/assets/styles/global.scss';
@use '@/assets/styles/styles.scss';

#FloatingInfo {
  position:sticky;
  margin-left: 20px;
  height: 2em;
  font-size: 0.8em;
  z-index: 9999;
}

#AppDate, #ServerDate {
  border: 2px solid black;
  padding: 5px;
  border-radius: 10px;
  background-color: white;
}
#Footer {
  font-size: 18px;
  font-family: NeueFrutigerWorld-Book, sans-serif;
  position: absolute;
  bottom: 0;
  width: 100%;
  color: black;
  z-index: 9;

}

#FooterData {
  height: 40px;
  z-index: 9;
}

.footerColor {
  background-color: global.$ngwcp_background;
}

.version,
.copyright {
  display: block;
  margin-top: 16px;
}

.version {
  float: left;
  margin-left: 20px;
}

.copyright {
  float: right;
  margin-right: 20px;
}
</style>
