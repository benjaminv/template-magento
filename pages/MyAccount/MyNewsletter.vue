<template>
  <SfTabs
    :open-tab="1"
    class="tab-orphan"
  >
    <SfTab :title="$t('My newsletter')">
      <p class="message">
        {{ $t('Set up newsletter') }}
      </p>
      <div class="form">
        <div class="form__checkbox-group">
          <SfCheckbox
            v-model="isSubscribed"
            v-e2e="'sign-up-newsletter'"
            :label="$t('Sign Up for Newsletter')"
            name="signupNewsletter"
            class="form__element"
          />
        </div>
        <SfButton
          class="form__button"
          @click="saveForm"
        >
          {{ $t('Save changes') }}
        </SfButton>
      </div>
      <p class="notice">
        {{ $t('Read and understand') }}
        <SfLink
          class="notice__link"
          href="#"
        >
          {{ $t('Privacy') }}
        </SfLink>
        and
        <SfLink
          class="notice__link"
          href="#"
        >
          {{ $t('Cookies Policy') }}
        </SfLink>
        {{ $t('Commercial information') }}
      </p>
    </SfTab>
  </SfTabs>
</template>

<script>
import {
  SfTabs, SfCheckbox, SfButton, SfLink,
} from '@storefront-ui/vue';
import { onSSR } from '@vue-storefront/core';
import { defineComponent, ref } from '@nuxtjs/composition-api';
import { useUser } from '@vue-storefront/magento';

export default defineComponent({
  name: 'MyNewsletter',
  components: {
    SfTabs,
    SfCheckbox,
    SfButton,
    SfLink,
  },
  setup() {
    const {
      user,
      load,
      updateUser,
      isAuthenticated,
    } = useUser();

    const isSubscribed = ref(!!user.value.is_subscribed);

    onSSR(async () => {
      await load();
    });

    const saveForm = async () => {
      if (isAuthenticated.value && !!user.value.email) {
        await updateUser({
          user: {
            is_subscribed: isSubscribed.value,
          },
        });
      }
    };

    return {
      isSubscribed,
      saveForm,
    };
  },
});
</script>

<style lang='scss' scoped>
.tab-orphan {
  @include for-mobile {
    --tabs-title-display: none;
    --tabs-content-padding: 0;
    --tabs-conent-border-width: 0;
  }
}

.form {
  &__element {
    margin: 0 0 var(--spacer-base) 0;

    &:last-child {
      margin: 0;
    }
  }

  &__checkbox-group {
    margin: 0 0 var(--spacer-xl) 0;
  }

  &__title {
    margin: 0 0 var(--spacer-base) 0;
  }

  &__button {
    --button-width: 100%;
    @include for-desktop {
      --button-width: 17.5rem;
    }
  }
}

.message {
  margin: 0 0 var(--spacer-xl) 0;
  color: var(--c-dark-variant);
}

.notice {
  margin: var(--spacer-base) 0 0 0;
  font-size: var(--font-size--xs);

  &__link {
    color: var(--c-primary);
    text-decoration: none;

    &:hover {
      color: var(--c-text);
    }
  }
}

</style>
