<template>
  <div class="container">
    <SfButton
      class="container__lang container__lang--selected"
      @click="isLangModalOpen = !isLangModalOpen"
    >
      <SfCharacteristic class="store-switcher">
        <template #title>
          <span class="desktop-only">{{ storeConfig.store_name }}</span>
        </template>
        <template #icon v-if="storeConfigGetters.getLocale(storeConfig)">
          <nuxt-img
            :src="`/icons/langs/${storeConfigGetters.getLocale(storeConfig)}.webp`"
            width="20"
            height="20"
            alt="Flag"
            class="language__flag"
          />
        </template>
      </SfCharacteristic>
    </SfButton>
    <LazyHydrate when-visible>
      <StoresModal
        v-if="isLangModalOpen"
        :store-config="storeConfig"
        :is-lang-modal-open="isLangModalOpen"
        @closeModal="isLangModalOpen = false"
      />
    </LazyHydrate>
  </div>
</template>

<script>
import LazyHydrate from 'vue-lazy-hydration';
import {
  useConfig,
  storeConfigGetters,
  storeGetters,
} from '@vue-storefront/magento';
import {
  SfButton,
  SfCharacteristic,
} from '@storefront-ui/vue';
import {
  ref,
  defineComponent,
} from '@nuxtjs/composition-api';
import StoresModal from '~/components/StoreSwitcher/StoresModal.vue';

export default defineComponent({
  name: 'StoreSwitcher',
  components: {
    StoresModal,
    SfButton,
    SfCharacteristic,
    LazyHydrate,
  },
  setup() {
    const {
      config,
    } = useConfig();

    const isLangModalOpen = ref(false);

    return {
      isLangModalOpen,
      storeConfig: config,
      storeConfigGetters,
      storeGetters,
    };
  },
});
</script>

<style lang="scss" scoped>
.language {
  &__flag {
    margin-right: var(--spacer-sm);
  }
}

.container__lang {
  --button-box-shadow: none;
  background: none;
  padding: 0 5px;
  display: flex;
  align-items: center;
  opacity: 0.5;
  border: none;

  &:hover,
  &--selected {
    opacity: 1;
  }
}

.store-switcher {
  text-transform: capitalize;
  color: var(--bottom-modal-title-color, var(--c-link));
  font-size: var(--font-size--sm);
}
</style>
