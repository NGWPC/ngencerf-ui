<template>
  <!-- Download CLI Box -->
  <div id="DownloadCliBox" ref="downloadCliBox" class="absolute h-auto rounded-lg">
    <div id="DownloadCliBoxContents" style="direction: ltr">
      <div class="text-right sticky top-0">
        <img alt="Close" title="Close" aria-label="Close" src="@/assets/styles/img/xclose.png" width="40"
          class="absolute cursor-pointer right-0 mt-2 mr-2" @click="closeDownloadCliBox" />
      </div>

      <div id="BoxContent">
        <div id="PgmName" class="inline-block font-bold ml-4 mt-3 text-lg">
          Download CLI
        </div>
        <hr class="mt-2" />

        <div class="grid grid-cols-3 gap-4">
          <div>
            <a href="{{ ngencerfBaseUrl }}/static/latest/windows/ngencerf.exe">Windows</a>
          </div>
          <div>
            <a href="{{ ngencerfBaseUrl }}/static/latest/macos/ngencerf">MacOS</a>
          </div>
          <div>
            <a href="{{ ngencerfBaseUrl }}/static/latest/linux/ngencerf">Linux</a>
          </div>
          <div class="col-span-3 font-bold">
            Installation Instructions
          </div>
          <div>
            <div class="font-bold">Windows</div>
            <ol>
              <li>Download ngencerf.exe.</li>
              <li>Move it to a directory on your PATH.</li>
              <li>Run ngencerf.exe from that directory, or ngencerf if installed on PATH.</li>
            </ol>
          </div>
          <div>
            <div class="font-bold">macOS</div>
            <ol>
              <li>Download ngencerf.</li>
              <li>Make it executable:<br/>
              <pre>chmod +x ngencerf</pre></li>
              <li>Run ./ngencerf from the download directory, or ngencerf if installed on PATH.</li>
            </ol>
          </div>
          <div>
            <div class="font-bold">Linux</div>
            <ol>
              <li>Download ngencerf.</li>
              <li>Make it executable:<br/>
              <pre>chmod +x ngencerf</pre></li>
              <li>Run ./ngencerf from the download directory, or ngencerf if installed on PATH.</li>
            </ol>
          </div>
          <div class="col-span-3">
            <div class="font-bold">Configure Server URL</div>

            <div>Before using the CLI, tell it which ngenCerf server to connect to:</div>

            <div><pre>ngencerf url add {{ ngencerfBaseUrl }}</pre></div>

            <div>The CLI saves this URL and uses it for future commands.</div>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useBackendConfig } from "@/composables/UseBackendConfig";
import { generalStore } from "@/stores/common/GeneralStore";

const { popupActive } = storeToRefs(generalStore());

const { ngencerfBaseUrl } = useBackendConfig();

const scrollHeight = ref<string>(); // Default height

const downloadCliBox = ref<HTMLElement | null>(null);

onMounted(async () => {
  setTimeout(() => {
    const resizeEvent = new Event('resize');
    window.dispatchEvent(resizeEvent);
  }, 250);
})

const closeDownloadCliBox = () => {
  useAccountEvent("downloadCliBoxEvent", "");
  popupActive.value = false;
}
</script>

<style lang="scss" scoped>
@use "@/assets/styles/global.scss";
@use "@/assets/styles/styles.scss";

#DownloadCliBox {
  right: 5px;
  top: 90px;
  border: 5px solid #ccc;
  z-index: 99;
  width: auto;
  max-width: 90%;
  background-color: white;
  resize: both;
  overflow-x: scroll;
  overflow-y: hidden;
  direction: rtl;
  min-width: 400px;
  min-height: 200px;

  hr {
    height: 2px;
    background-color: black;
  }

  .hlink {
    text-decoration: underline;
    color: blue;
  }

}
</style>
