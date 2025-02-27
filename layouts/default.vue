<template>
  <div>
    <IconSprite />
    <CartSidebar v-if="isCartSidebarOpen" />
    <WishlistSidebar v-if="isWishlistSidebarOpen" />
    <LoginModal v-if="isLoginModalOpen" />
    <LazyHydrate when-visible>
      <Notification />
    </LazyHydrate>
    <TopBar class="desktop-only" />
    <AppHeader />
    <div id="layout">
      <nuxt :key="route.fullPath" />
    </div>
    <BottomNavigation />
    <LoadWhenVisible>
      <AppFooter />
    </LoadWhenVisible>
  </div>
</template>

<script>
import LazyHydrate from 'vue-lazy-hydration';
import {
  useRoute, defineComponent, useAsync,
} from '@nuxtjs/composition-api';
import {
  useUser,
} from '@vue-storefront/magento';
import useUiState from '~/composables/useUiState.ts';
import LoadWhenVisible from '~/components/utils/LoadWhenVisible';
import { useMagentoConfiguration } from '~/composables/useMagentoConfiguration';
import AppHeader from '~/components/AppHeader.vue';
import BottomNavigation from '~/components/BottomNavigation.vue';
import IconSprite from '~/components/General/IconSprite.vue';
import TopBar from '~/components/TopBar';

export default defineComponent({
  name: 'DefaultLayout',

  components: {
    LoadWhenVisible,
    LazyHydrate,
    AppHeader,
    BottomNavigation,
    IconSprite,
    TopBar,
    AppFooter: () => import(/* webpackPrefetch: true */ '~/components/AppFooter.vue'),
    CartSidebar: () => import(/* webpackPrefetch: true */ '~/components/CartSidebar.vue'),
    WishlistSidebar: () => import(/* webpackPrefetch: true */ '~/components/WishlistSidebar.vue'),
    LoginModal: () => import(/* webpackPrefetch: true */ '~/components/LoginModal.vue'),
    Notification: () => import(/* webpackPrefetch: true */ '~/components/Notification'),
  },

  setup() {
    const route = useRoute();
    const { load: loadUser } = useUser();
    const { loadConfiguration } = useMagentoConfiguration();
    const { isCartSidebarOpen, isWishlistSidebarOpen, isLoginModalOpen } = useUiState();

    useAsync(async () => {
      await Promise.all([loadConfiguration(), loadUser()]);
    });

    return {
      isCartSidebarOpen,
      isWishlistSidebarOpen,
      isLoginModalOpen,
      route,
    };
  },
});
</script>

<style lang="scss">
@import "~@storefront-ui/vue/styles";

#layout {
  box-sizing: border-box;
  @include for-desktop {
    max-width: 1270px;
    margin: auto;
  }
}

.no-scroll {
  overflow: hidden;
  height: 100vh;
}

// Reset CSS
html {
  width: auto;
  @include for-mobile {
    overflow-x: hidden;
  }
}

body {
  overflow-x: hidden;
  color: var(--c-text);
  font-size: var(--font-size--base);
  font-family: var(--font-family--primary);
  margin: 0;
  padding: 0;
}

a {
  text-decoration: none;
  color: var(--c-link);

  &:hover {
    color: var(--c-link-hover);
  }
}

h1 {
  font-family: var(--font-family--secondary);
  font-size: var(--h1-font-size);
  line-height: 1.6;
  margin: 0;
}

h2 {
  font-family: var(--font-family--secondary);
  font-size: var(--h2-font-size);
  line-height: 1.6;
  margin: 0;
}

h3 {
  font-family: var(--font-family--secondary);
  font-size: var(--h3-font-size);
  line-height: 1.6;
  margin: 0;
}

h4 {
  font-family: var(--font-family--secondary);
  font-size: var(--h4-font-size);
  line-height: 1.6;
  margin: 0;
}
</style>
