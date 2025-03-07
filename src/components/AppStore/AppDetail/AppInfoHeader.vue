<template>
  <div class="pb-4 app-header is-flex">
    <div class="mr-5 header-icon">
      <b-image :key="appDetailData.icon" :src="appDetailData.icon"
        :src-fallback="require('@/assets/img/app/default.svg')" class="is-128x128 icon-shadow app-icon"
        custom-class="image is-128x128" webp-fallback=".jpg">
      </b-image>
    </div>

    <div class="is-flex-grow-1 is-flex is-align-items-center">
      <div>
        <h4 class="title store-title is-4">{{ i18n(appDetailData.title) }}</h4>
        <p class="mb-3 subtitle is-size-14px two-line">{{ i18n(appDetailData.tagline) }}</p>
        <p class="mb-2 description">
          <b-button v-if="installedList.includes(appDetailData.id)" :loading="appDetailData.id == currentInstallId"
            rounded size="is-normal" type="is-primary" @click="openThirdAppInStore(appDetailData)">
            {{ $t("Open") }}
          </b-button>

          <b-dropdown v-else :disabled="unusable" :triggers="['click']" :mobile-modal="false" @click.stop>
            <template #trigger>
              <div>
                <b-button class="pr-2" style="border-top-right-radius: 0px; border-bottom-right-radius: 0px"
                  :disabled="unusable" :loading="appDetailData.id == currentInstallId" rounded size="is-normal"
                  type="is-primary">
                  <div @click.self="
                    $emit('install', appDetailData.id, appDetailData);
                  $messageBus('appstore_install', i18n(appDetailData.title));
                  " class="custom-install-button-content">
                    {{ $t("Install") }}
                  </div>
                </b-button>
                <b-button class="pl-2 pr-3" style="border-top-left-radius: 0; border-bottom-left-radius: 0" rounded
                  size="is-normal" type="is-primary">
                  <div class="casa-down-outline custom-install-dropdown-trigger"></div>
                </b-button>
              </div>
            </template>
            <b-button :disabled="unusable" :loading="appDetailData.id == currentInstallId" rounded size="is-normal"
              type="is-primary" @click="openConfigPanle">
              {{ $t("Custom Install") }}
            </b-button>
          </b-dropdown>
        </p>

        <p v-if="unusable"
          class="pr-2 has-background-red-tertiary has-text-red has-text-full-04 _is-normal is-flex is-align-items-center font"
          style="width: fit-content; height: 1.5rem; border-radius: 0.25rem">
          <label class="ml-2 mr-1 is-flex">
            <b-icon class="is-16x16" custom-size="casa-19px" icon="close" pack="casa"></b-icon>
          </label>
          {{ $t("Not compatible with {arch} devices.", { arch: archTitle }) }}
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { inject } from "vue";
import { usei18n } from "@/composables/usei18n";
import messageBus from "@/events";
import YAML from "yaml";
import { useOpenAppInStore } from "@/composables/useOpenApp";

const switchAppPanelToAppConfigContent = inject("switchAppPanelToAppConfigContent");
const openThirdAppInStore = useOpenAppInStore();
const props = defineProps({
  appDetailData: {
    type: Object,
    default: () => { },
  },
  installedList: {
    type: Array,
    default: () => { },
  },
  unusable: {
    type: Boolean,
    default: false,
  },
  currentInstallId: {
    type: String,
    default: "",
  },
  archTitle: {
    type: String,
    default: "",
  },
});

const emit = defineEmits(["install"]);

const { i18n } = usei18n();

const handleInstallBtnClick = () => {
  emit("install");
  messageBus("appstore_install", i18n(props.appDetailData.title));
};

function openConfigPanle() {
  // this.$emit('switchAppPanelToAppConfigContent', YAML.stringify(this.appDetailData.compose))
  console.log("openConfigPanle", props.appDetailData);
  switchAppPanelToAppConfigContent(YAML.stringify(props.appDetailData.compose));
}

defineExpose({
  i18n,
});
</script>

<style lang="scss" scoped>
.app-header {
  position: relative;
}

::v-deep .dropdown-content {
  box-shadow: none;
  padding: 0;
}

@media screen and (max-width: 480px) {
  .app-header {
    position: relative;

    .header-icon {
      .is-128x128 {
        height: 96px;
        width: 96px;
      }
    }

    .store-title {
      font-size: 1.125rem;
    }

    .subtitle {
      font-size: 0.75rem;
      margin-bottom: 0.75rem;
    }

    .description {
      .button {
        font-size: 0.75rem;
      }
    }

    .custom-install-dropdown-trigger {
      /* width: 3rem; */
      height: 1.125rem;
    }
  }
}

.custom-install-dropdown-trigger {
  /* width: 3rem; */
  height: 1.5rem;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
<style lang="scss">
/* // version dropdown css */
.custom-install-button {
  span {
    display: flex;

    .custom-install-button-content {
      display: flex;
      flex-shrink: 0;
      padding-left: 1.25rem;
      padding-top: 0.25rem;
      padding-bottom: 0.25rem;
    }

    .custom-install-dropdown-trigger {
      /* width: 3rem; */
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .dropdown-menu,
    .dropdown-content {
      box-shadow: none;
    }

    .dropdown-content .button {
      display: flex;
    }
  }
}
</style>
