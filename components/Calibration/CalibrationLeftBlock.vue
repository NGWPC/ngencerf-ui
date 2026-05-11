<template>
  <!-- CalibrationLeftBlock.vue -->
  <div>
    <Tabs @tabNumber="tabChanged" :call-tab-validator="validateCurrentTab" :call-tab-restore="restoreCurrentTab"/>
    
    <div v-if="activeTab === 1">
      <CalibrationCalibrationRunsTab/>
    </div> 
    <div v-else-if="activeTab === 2">
      <CalibrationHeadwaterBasinGage ref="tabRef"/>
    </div>
    <div v-else-if="activeTab === 3">
      <CalibrationFormulation ref="tabRef"/>
    </div>
    <div v-else-if="activeTab === 4">
      <CalibrationTuningControls ref="tabRef"/>
    </div>
    <div v-else-if="activeTab === 5">
      <CalibrationOptimizationMetrics ref="tabRef"/>
    </div>
    <div v-else-if="activeTab === 6">
      <CalibrationRunStatus/>
    </div>
    
  </div>

</template>

<script setup lang="ts">

import { generalStore } from "@/stores/common/GeneralStore";

import Tabs from '@/components/Common/Tabs.vue'
import CalibrationHeadwaterBasinGage from '@/components/Calibration/HeadwaterBasinGage.vue';
import CalibrationFormulation from '@/components/Calibration/Formulation.vue';
import CalibrationTuningControls from '@/components/Calibration/TuningControls.vue';
import CalibrationOptimizationMetrics from '@/components/Calibration/OptimizationMetrics.vue';
import CalibrationRunStatus from '@/components/Calibration/RunStatus.vue';
import CalibrationCalibrationRunsTab from '@/components/Calibration/PreviousCalibrationRuns.vue';
import { ThemeUtils } from "@primeuix/styled";

const { getCalibrationTabIndex, setCalibrationTabIndex } = generalStore();

const activeTab = ref(getCalibrationTabIndex());

// Activate new tab
const tabChanged = (tabNum: number) => {
  if (activeTab.value !== tabNum) {
    activeTab.value = tabNum;
    setCalibrationTabIndex(tabNum);
  }
};

const tabRef = ref(null);

function validateCurrentTab(ele?: HTMLElement) {
  if (typeof tabRef?.value?.validateTab === 'function') {
    return tabRef.value.validateTab(ele);
  }
  return true;
}
function restoreCurrentTab() {
  if (typeof tabRef?.value?.restoreTab === 'function') {
    return tabRef?.value?.restoreTab();
  }
  return true;
}
</script>
