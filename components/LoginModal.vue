<template>
  <SfModal
    v-e2e="'login-modal'"
    :visible="isLoginModalOpen"
    class="modal"
    @close="closeModal"
  >
    <template #modal-bar>
      <SfBar
        class="sf-modal__bar smartphone-only"
        :close="true"
        :title="barTitle"
        @click:close="closeModal"
      />
    </template>
    <transition
      name="sf-fade"
      mode="out-in"
    >
      <div v-if="isLogin">
        <ValidationObserver
          v-slot="{ handleSubmit }"
          key="log-in"
        >
          <form
            class="form"
            @submit.prevent="handleSubmit(handleLogin)"
          >
            <ValidationProvider
              v-slot="{ errors }"
              rules="required|email"
            >
              <SfInput
                v-model="form.username"
                v-e2e="'login-modal-email'"
                :valid="!errors[0]"
                :error-message="$t(errors[0])"
                name="email"
                :label="$t('Your email')"
                class="form__element"
              />
            </ValidationProvider>
            <ValidationProvider
              v-slot="{ errors }"
              rules="required"
            >
              <SfInput
                v-model="form.password"
                v-e2e="'login-modal-password'"
                :valid="!errors[0]"
                :error-message="$t(errors[0])"
                name="password"
                :label="$t('Password')"
                type="password"
                has-show-password
                class="form__element"
              />
            </ValidationProvider>
            <recaptcha v-if="isRecaptchaEnabled" />
            <div v-if="error.login">
              {{ $t(error.login) }}
            </div>
            <SfButton
              v-e2e="'login-modal-submit'"
              type="submit"
              class="sf-button--full-width form__button"
              :disabled="loading"
            >
              <SfLoader
                :class="{ loader: loading }"
                :loading="loading"
              >
                <div>{{ $t('Login') }}</div>
              </SfLoader>
            </SfButton>
          </form>
        </ValidationObserver>
        <div class="action">
          <SfButton
            class="sf-button--text"
            @click="setIsForgottenValue(true)"
          >
            {{ $t('Forgotten password?') }}
          </SfButton>
        </div>
        <div class="bottom">
          <p class="bottom__paragraph">
            {{ $t('No account') }}
          </p>
          <SfButton
            class="sf-button--text"
            @click="setIsLoginValue(false)"
          >
            {{ $t('Register today') }}
          </SfButton>
        </div>
      </div>
      <div v-else-if="isForgotten">
        <p>{{ $t('Forgot Password') }}</p>
        <ValidationObserver
          v-slot="{ handleSubmit }"
          key="log-in"
        >
          <form
            class="form"
            @submit.prevent="handleSubmit(handleForgotten)"
          >
            <ValidationProvider
              v-slot="{ errors }"
              rules="required|email"
            >
              <SfInput
                v-model="form.username"
                v-e2e="'forgot-modal-email'"
                :valid="!errors[0]"
                :error-message="$t(errors[0])"
                name="email"
                :label="$t('Forgot Password Modal Email')"
                class="form__element"
              />
            </ValidationProvider>
            <recaptcha v-if="isRecaptchaEnabled" />
            <div v-if="forgotPasswordError.request">
              {{ $t('It was not possible to request a new password, please check the entered email address.') }}
            </div>
            <SfButton
              v-e2e="'forgot-modal-submit'"
              type="submit"
              class="sf-button--full-width form__button"
              :disabled="forgotPasswordLoading"
            >
              <SfLoader
                :class="{ loader: forgotPasswordLoading }"
                :loading="forgotPasswordLoading"
              >
                <div>{{ $t('Reset Password') }}</div>
              </SfLoader>
            </SfButton>
          </form>
        </ValidationObserver>
      </div>
      <div
        v-else-if="isThankYouAfterForgotten"
        class="thank-you"
      >
        <i18n
          tag="p"
          class="thank-you__paragraph"
          path="forgotPasswordConfirmation"
        >
          <span class="thank-you__paragraph--bold">{{ userEmail }}</span>
        </i18n>
        <p class="thank-you__paragraph">
          {{ $t('Thank You Inbox') }}
        </p>
      </div>
      <div
        v-else
        class="form"
      >
        <ValidationObserver
          v-slot="{ handleSubmit, invalid }"
          key="sign-up"
        >
          <form
            class="form"
            autocomplete="off"
            @submit.prevent="handleSubmit(handleRegister)"
          >
            <ValidationProvider
              v-slot="{ errors }"
              rules="required|email"
            >
              <SfInput
                v-model="form.email"
                v-e2e="'login-modal-email'"
                :valid="!errors[0]"
                :error-message="$t(errors[0])"
                name="email"
                :label="$t('Your email')"
                class="form__element"
              />
            </ValidationProvider>
            <ValidationProvider
              v-slot="{ errors }"
              rules="required"
            >
              <SfInput
                v-model="form.firstName"
                v-e2e="'login-modal-firstName'"
                :valid="!errors[0]"
                :error-message="$t(errors[0])"
                name="first-name"
                :label="$t('First Name')"
                class="form__element"
              />
            </ValidationProvider>
            <ValidationProvider
              v-slot="{ errors }"
              rules="required"
            >
              <SfInput
                v-model="form.lastName"
                v-e2e="'login-modal-lastName'"
                :valid="!errors[0]"
                :error-message="$t(errors[0])"
                name="last-name"
                :label="$t('Last Name')"
                class="form__element"
              />
            </ValidationProvider>
            <ValidationProvider
              v-slot="{ errors }"
              rules="required|password"
            >
              <SfInput
                v-model="form.password"
                v-e2e="'login-modal-password'"
                :valid="!errors[0]"
                :error-message="$t(errors[0])"
                name="password"
                :label="$t('Password')"
                type="password"
                has-show-password
                class="form__element"
              />
            </ValidationProvider>
            <SfCheckbox
              v-model="isSubscribed"
              v-e2e="'sign-up-newsletter'"
              :label="$t('Sign Up for Newsletter')"
              name="signupNewsletter"
              class="form__element"
            />
            <ValidationProvider
              v-slot="{ errors }"
              :rules="{ required: { allowFalse: false } }"
            >
              <SfCheckbox
                v-model="createAccount"
                v-e2e="'login-modal-create-account'"
                :valid="!errors[0]"
                :error-message="$t(errors[0])"
                name="create-account"
                :label="$t('I want to create an account')"
                class="form__element"
              />
            </ValidationProvider>
            <recaptcha v-if="isRecaptchaEnabled" />
            <div v-if="error.register">
              {{ $t(error.register) }}
            </div>
            <SfButton
              v-e2e="'login-modal-submit'"
              type="submit"
              class="sf-button--full-width form__button"
              :disabled="loading || !createAccount || invalid"
            >
              <SfLoader
                :class="{ loader: loading }"
                :loading="loading"
              >
                <div>{{ $t('Create an account') }}</div>
              </SfLoader>
            </SfButton>
          </form>
        </ValidationObserver>
        <div class="action">
          {{ $t('or') }}
          <SfButton
            v-e2e="'login-modal-login-to-your-account'"
            class="sf-button--text"
            @click="setIsLoginValue(true)"
          >
            {{ $t('login in to your account') }}
          </SfButton>
        </div>
      </div>
    </transition>
  </SfModal>
</template>
<script>
import {
  ref,
  watch,
  reactive,
  defineComponent,
  computed,
  useContext,
} from '@nuxtjs/composition-api';
import {
  SfModal,
  SfInput,
  SfButton,
  SfCheckbox,
  SfLoader,
  SfBar,
} from '@storefront-ui/vue';
import { ValidationProvider, ValidationObserver, extend } from 'vee-validate';
import { required, email } from 'vee-validate/dist/rules';
import {
  useUser, useForgotPassword, useWishlist, useCart,
} from '@vue-storefront/magento';
import { useUiState } from '~/composables';
import { customerPasswordRegExp, invalidPasswordMsg } from '~/helpers/customer/regex';

extend('email', {
  ...email,
  message: 'Invalid email',
});

extend('required', {
  ...required,
  message: 'This field is required',
});

extend('password', {
  message: invalidPasswordMsg,
  validate: (value) => customerPasswordRegExp.test(value),
});

export default defineComponent({
  name: 'LoginModal',
  components: {
    SfModal,
    SfInput,
    SfButton,
    SfCheckbox,
    SfLoader,
    ValidationProvider,
    ValidationObserver,
    SfBar,
  },
  setup() {
    const { isLoginModalOpen, toggleLoginModal } = useUiState();
    const isSubscribed = ref(false);
    const form = ref({});
    const isLogin = ref(true);
    const createAccount = ref(false);
    const rememberMe = ref(false);
    const isForgotten = ref(false);
    const isThankYouAfterForgotten = ref(false);
    const userEmail = ref('');
    const { $recaptcha, $config } = useContext();
    const isRecaptchaEnabled = ref(typeof $recaptcha !== 'undefined' && $config.isRecaptcha);

    const {
      register,
      login,
      loading,
      error: userError,
    } = useUser();

    const { load: loadCart } = useCart();
    const { loadItemsCount } = useWishlist('GlobalWishlist');
    const { request, error: forgotPasswordError, loading: forgotPasswordLoading } = useForgotPassword();

    const barTitle = computed(() => {
      if (isLogin.value) {
        return 'Sign in';
      } if (isForgotten.value || isThankYouAfterForgotten.value) {
        return 'Reset Password';
      }
      return 'Register';
    });

    const error = reactive({
      login: null,
      register: null,
    });

    const resetErrorValues = () => {
      error.login = null;
      error.register = null;
    };

    watch(isLoginModalOpen, () => {
      if (isLoginModalOpen) {
        form.value = {};
        resetErrorValues();
      }
    });

    const setIsLoginValue = (value) => {
      resetErrorValues();
      isLogin.value = value;
    };

    const setIsForgottenValue = (value) => {
      resetErrorValues();
      isForgotten.value = value;
      isLogin.value = !value;
      isThankYouAfterForgotten.value = value;
    };

    const closeModal = () => {
      setIsForgottenValue(false);
      setIsLoginValue(true);
      toggleLoginModal();
    };

    const handleForm = (fn) => async () => {
      resetErrorValues();

      if (isRecaptchaEnabled.value) {
        $recaptcha.init();
      }

      if (isRecaptchaEnabled.value) {
        const recaptchaToken = await $recaptcha.getResponse();
        form.value.recaptchaInstance = $recaptcha;

        await fn({
          user: {
            ...form.value,
            is_subscribed: isSubscribed.value,
            recaptchaToken,
          },
        });
      } else {
        await fn({
          user: {
            ...form.value,
            is_subscribed: isSubscribed.value,
          },
        });
      }

      const hasUserErrors = userError.value.register || userError.value.login;
      if (hasUserErrors) {
        error.login = userError.value.login?.message;
        error.register = userError.value.register?.message;
        return;
      }
      toggleLoginModal();

      if (isRecaptchaEnabled.value) {
        // reset recaptcha
        $recaptcha.reset();
      }
    };

    const handleRegister = async () => handleForm(register)();

    const handleLogin = async () => {
      await handleForm(login)();
      await Promise.all([loadItemsCount('GlobalWishlist'), loadCart()]);
    };

    const handleForgotten = async () => {
      userEmail.value = form.value.username;

      if (isRecaptchaEnabled.value) {
        $recaptcha.init();
      }

      if (isRecaptchaEnabled.value) {
        const recaptchaToken = await $recaptcha.getResponse();

        await request({ email: userEmail.value, recaptchaToken });
      } else {
        await request({ email: userEmail.value });
      }

      if (!forgotPasswordError.value.request) {
        isThankYouAfterForgotten.value = true;
        isForgotten.value = false;
      }

      if (isRecaptchaEnabled.value) {
        // reset recaptcha
        $recaptcha.reset();
      }
    };

    return {
      barTitle,
      closeModal,
      createAccount,
      error,
      forgotPasswordError,
      forgotPasswordLoading,
      form,
      handleForgotten,
      handleLogin,
      handleRegister,
      isForgotten,
      isLogin,
      isLoginModalOpen,
      isSubscribed,
      isThankYouAfterForgotten,
      loading,
      rememberMe,
      setIsForgottenValue,
      setIsLoginValue,
      userEmail,
      userError,
      isRecaptchaEnabled,
    };
  },
});
</script>

<style lang="scss" scoped>

.modal {
  --modal-index: 3;
  --overlay-z-index: 3;
}

.form {
  margin-top: var(--spacer-sm);

  &__element {
    margin: 0 0 var(--spacer-xl) 0;
  }
}

.action {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: var(--spacer-xl) 0 var(--spacer-xl) 0;
  font: var(--font-weight--light) var(--font-size--base) / 1.6 var(--font-family--secondary);

  & > * {
    margin: 0 0 0 var(--spacer-xs);
  }
}

.action {
  margin: var(--spacer-xl) 0 var(--spacer-xl) 0;
}

.checkbox {
  margin-bottom: var(--spacer-2xl);
}

.bottom {
  text-align: center;
  margin-bottom: var(--spacer-lg);
  font-size: var(--h3-font-size);
  font-weight: var(--font-weight--semibold);
  font-family: var(--font-family--secondary);

  &__paragraph {
    color: var(--c-primary);
    margin: 0 0 var(--spacer-base) 0;
    @include for-desktop {
      margin: 0;
    }
  }
}
.thank-you {
  &__paragraph {
    &--bold {
      font-weight: var(--font-weight--semibold);
    }
  }
}
</style>
