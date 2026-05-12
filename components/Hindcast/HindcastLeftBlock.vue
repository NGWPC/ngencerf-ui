<template> 
  <!-- HindcastLeftBlock.vue -->
  <div>
    <Tabs @tabNumber="tabChanged" ref="navRef" :call-tab-validator="validateCurrentTab" />
    <div class="shrink-0">
      <span v-if="activeTab === 1">
        <PreviousCalibrationRuns/>
      </span>
      <span v-else-if="activeTab === 2">
        <HindcastRunsTab/>
      </span>
      <span v-else-if="activeTab === 3">
        <SetupHindcastTab ref="tabRef"/>
      </span>
      <span v-else-if="activeTab === 4">
        <HindcastRunStatusTab ref="tabRef"/>
      </span>
      <span v-else-if="activeTab === 5">
        <HindcastResultsTab/>
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
import PreviousCalibrationRuns from "./PreviousCalibrationRuns.vue"
import HindcastRunsTab from "./HindcastRunsTab.vue"
import SetupHindcastTab from './SetupHindcastTab.vue';
import HindcastRunStatusTab from './HindcastRunStatusTab.vue';
import HindcastResultsTab from './HindcastResultsTab.vue';
import VerificationRunsTab from "./VerificationRunsTab.vue"
import VerificationRunStatusTab from "./VerificationRunStatusTab.vue"
import VerificationResultsTab from "./VerificationResultsTab.vue"

const { tabRef, navRef } = storeToRefs(generalStore());
const { getHindcastTabIndex, setHindcastTabIndex, validateCurrentTab } = generalStore();

// Default to Tab 1, PreviousCalibrationRuns
const activeTab = ref(getHindcastTabIndex());

// Activate new tab
const tabChanged = (tabNum: number) => {
  if (activeTab.value !== tabNum) {
    activeTab.value = tabNum;
    setHindcastTabIndex(tabNum);
  } 
};
</script>
