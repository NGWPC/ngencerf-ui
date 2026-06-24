<template>
  <!-- ForecastLeftBlock.vue -->
  <div>
    <Tabs @tabNumber="tabChanged" ref="navRef" :call-tab-validator="validateCurrentTab" />
    <div class="shrink-0">
      <span v-if="activeTab === 1">
        <PreviousCalibrationRuns :call-go-to-tab="currentTabNavGo" />
      </span>
      <span v-else-if="activeTab === 2">
        <ForecastRunsTab :call-go-to-tab="currentTabNavGo"/>
      </span>
      <span v-else-if="activeTab === 3">
        <SetupForecastTab ref="tabRef" :call-go-to-tab="currentTabNavGo"/>
      </span>
      <span v-else-if="activeTab === 4">
        <ForecastRunStatusTab ref="tabRef" :call-go-to-tab="currentTabNavGo"/>
      </span>
      <span v-else-if="activeTab === 5">
        <ForecastResultsTab/>
      </span>
      <span v-if="activeTab === 6">
        <VerificationRunsTab :call-go-to-tab="currentTabNavGo"/>
      </span>
      <span v-else-if="activeTab === 7">
        <VerificationRunStatusTab ref="tabRef" :call-go-to-tab="currentTabNavGo"/>
      </span>
      <span v-else-if="activeTab === 8">
        <VerificationResultsTab/>
      </span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { generalStore } from "@/stores/common/GeneralStore";
import { useForecastStore } from "@/stores/forecast/ForecastStore";
import { useVerificationStore } from "~/stores/forecast/VerificationStore";

import Tabs from '@/components/Common/Tabs.vue'
import PreviousCalibrationRuns from '@/components/Forecast/PreviousCalibrationRuns.vue';
import ForecastRunsTab from './ForecastRunsTab.vue';
import SetupForecastTab from './SetupForecastTab.vue';
import ForecastRunStatusTab from './ForecastRunStatusTab.vue';
import ForecastResultsTab from './ForecastResultsTab.vue';
import VerificationRunsTab from "./VerificationRunsTab.vue"
import VerificationRunStatusTab from "./VerificationRunStatusTab.vue"
import VerificationResultsTab from "./VerificationResultsTab.vue"

const { tabRef, navRef } = storeToRefs(generalStore());
const { getForecastTabIndex, setForecastTabIndex, validateCurrentTab, currentTabNavGo, showCurrentTabNavDialog } = generalStore();
const { forecastJobId } = storeToRefs(useForecastStore());
const { verificationJobId } = storeToRefs(useVerificationStore());

// Default to Tab 1, PreviousCalibrationRuns
const activeTab = ref(getForecastTabIndex());

// Activate new tab
const tabChanged = (tabNum: number) => {
  if (activeTab.value !== tabNum) {
    activeTab.value = tabNum;
    setForecastTabIndex(tabNum);
  } 
};

onUnmounted(() => {
  forecastJobId.value = undefined;
  verificationJobId.value = undefined;
})
</script>
