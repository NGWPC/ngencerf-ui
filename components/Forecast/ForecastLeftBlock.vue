<template>
  <!-- ForecastLeftBlock.vue -->
  <div>
    <Tabs @tabNumber="tabChanged" ref="navRef" :call-tab-validator="validateCurrentTab" />
    <div class="shrink-0">
      <span v-if="activeTab === 1">
        <PreviousCalibrationRuns/>
      </span>
      <span v-else-if="activeTab === 2">
        <ForecastRunsTab/>
      </span>
      <span v-else-if="activeTab === 3">
        <SetupForecastTab ref="tabRef"/>
      </span>
      <span v-else-if="activeTab === 4">
        <ForecastRunStatusTab ref="tabRef"/>
      </span>
      <span v-else-if="activeTab === 5">
        <ForecastResultsTab/>
      </span>
      <span v-if="activeTab === 6">
        <VerificationRunsTab/>
      </span>
      <span v-else-if="activeTab === 7">
        <VerificationRunStatusTab ref="tabRef"/>
      </span>
      <span v-else-if="activeTab === 8">
        <VerificationResultsTab/>
      </span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { generalStore } from "@/stores/common/GeneralStore";

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
const { getForecastTabIndex, setForecastTabIndex, validateCurrentTab } = generalStore();

// Default to Tab 1, PreviousCalibrationRuns
const activeTab = ref(getForecastTabIndex());

// Activate new tab
const tabChanged = (tabNum: number) => {
  if (activeTab.value !== tabNum) {
    activeTab.value = tabNum;
    setForecastTabIndex(tabNum);
  } 
};
</script>
