<template>
  <nav id="topnav" class="border-bottom position-fixed width-full">
    <div
      class="d-flex flex-items-center px-5"
      style="height: 96px;padding-left:15px !important"
    >
      <div class="flex-auto d-flex flex-items-center">
        <a class="d-block d-xl-none text-white" @click="toggleSidebar">
          <Icon name="menu" size="28" class="mr-3" />
        </a>
        <!-- <router-link class="brand" :to="'/'">
          <img class="logo" src="@/assets/symmetricIcon.svg">
          <div class="title">Symmetric</div>
          <br />
        </router-link>
        <span class="network"> xDai</span> -->
        <router-link
          :to="{ name: 'home' }"
          class="d-flex"
          style="padding-top: 2px;"
        >
          <img class="logo" src="@/assets/symmetricIcon.svg" />
          <span class="title">Symmetric </span>
          <!-- <span
            style="letter-spacing: 1px; font-size: 24px; font-weight: 600; color: #FB6706;"
            v-text="'Symmetric '"
          /> -->
          <!-- <span
            style="letter-spacing: 1px; font-size: 14px; font-weight: 600; color: #ffffff;"
            v-text="' on ' + config.network "
          /> -->
        </router-link>
        <div class="ml-6 !important" style="color:#fb6706;">
          Symmetric is currently in open public testing. During this testing period, no SYMM reward tokens will be distributed
        </div>
      </div>
      <div :key="web3.account">
        <UiButton
          v-if="$auth.isAuthenticated && !wrongNetwork"
          @click="modalOpen.account = true"
          :loading="loading || ui.authLoading"
        >
          <Avatar :address="web3.account" size="16" class="ml-n1 mr-n1" />
          <span v-if="web3.name" v-text="web3.name" class="hide-sm ml-2 pl-1" />
          <span
            v-else
            v-text="_shortenAddress(web3.account)"
            class="hide-sm ml-2 pl-1"
          />
        </UiButton>
        <UiButton
          v-if="$auth.isAuthenticated && wrongNetwork"
          class="button-yello px-5"
          @click="modalOpen.help = true"
        >
          <Icon name="info" class="ml-n2 mr-1 v-align-middle" />
          Help
        </UiButton>
        <UiButton
          v-if="$auth.isAuthenticated && wrongNetwork"
          class="button-red shake-horizontal"
        >
          <Icon name="warning" class="ml-n2 mr-1 v-align-middle" />
          {{ $t('wrongNetwork') }}
        </UiButton>
        <UiButton
          v-if="!$auth.isAuthenticated"
          @click="modalOpen.account = true"
          :loading="loading || ui.authLoading"
          class="button-primary"
        >
          {{ $t('connectWallet') }}
        </UiButton>
        <router-link
          v-if="$auth.isAuthenticated && !wrongNetwork && !ui.authLoading"
          :to="{ name: 'wallet' }"
          class="ml-2"
        >
          <UiButton class="v-align-bottom p-0">
            <Icon name="wallet" size="20" class="mx-3" />
          </UiButton>
        </router-link>
        <UiButton
          v-if="myPendingTransactions.length"
          @click="modalOpen.activity = true"
          class="button-primary ml-2"
        >
          {{ myPendingTransactions.length }}
        </UiButton>
      </div>
    </div>
    <portal to="modal">
      <ModalAccount
        :open="modalOpen.account"
        @close="modalOpen.account = false"
        @login="handleLogin"
      />
      <ModalHelpConnect
        :topic="metamask"
        :open="modalOpen.help"
        @close="modalOpen.help = false"
      />
      <ModalActivity
        :open="modalOpen.activity"
        @close="modalOpen.activity = false"
        @login="handleLogin"
      />
    </portal>
  </nav>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';

export default {
  data() {
    return {
      loading: false,
      modalOpen: {
        account: false,
        activity: false,
        help: false
      }
    };
  },
  computed: {
    ...mapGetters(['myPendingTransactions']),
    wrongNetwork() {
      return (
        this.config.chainId !== this.web3.injectedChainId &&
        !this.ui.authLoading &&
        !this.loading
      );
    }
  },
  methods: {
    ...mapActions(['toggleSidebar']),
    ...mapActions(['login']),
    async handleLogin(connector) {
      this.modalOpen.account = false;
      this.loading = true;
      await this.login(connector);
      this.loading = false;
    }
  }
};
</script>

<style scoped lang="scss">
@import '../vars';
.border-bottom {
  box-shadow: 0 5px 10px 0 rgba(0, 0, 0, 0.15);
}
.network {
  color: #48a9a6 !important;
  font-size: 12px;
  font-weight: 200;
  align-self: flex-end;
}
.brand {
  margin-left: 20px;
  display: flex;
  align-items: center;
}
.title {
  margin-left: 2px;
  align-self: center;
  letter-spacing: 1px;
  font-size: 24px;
  font-weight: 500;
  color: white !important;
}
#topnav {
  z-index: 10;
  background-color: $panel-background;
}
</style>
