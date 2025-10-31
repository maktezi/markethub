<template>
  <NuxtLoadingIndicator />
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
  <UNotifications />
</template>
<script setup lang="ts">
import { useMediaQuery } from "@vueuse/core";

const isMobile = ref(false);
const auth = useAuthStore();
const isAdmin = computed(() => auth.user?.is_admin);

provide("isMobile", isMobile);
provide("isAdmin", isAdmin);

onMounted(() => {
  const mediaQuery = useMediaQuery("(max-width: 768px)");
  isMobile.value = mediaQuery.value;

  watch(mediaQuery, (newValue) => {
    isMobile.value = newValue;
  });
});

// --- Google Tag Manager + consent logic ---
if (process.client) {
  useHead({
    script: [
      // --- Google Tag Manager Loader ---
      {
        hid: "gtm-loader",
        innerHTML: `
          (function(w,d,s,l,i){
            w[l]=w[l]||[];
            w[l].push({'gtm.start': new Date().getTime(), event:'gtm.js'});
            const f=d.getElementsByTagName(s)[0];
            const j=d.createElement(s);
            const dl=l!='dataLayer'?'&l='+l:'';
            j.async=true;
            j.src='https://www.googletagmanager.com/gtm.js?id='+i+dl;
            f.parentNode.insertBefore(j,f);
          })(window,document,'script','dataLayer','GTM-WL9SX8HD');
        `,
        type: "text/javascript",
      },
    ],
    noscript: [
      {
        innerHTML: `
          <iframe src="https://www.googletagmanager.com/ns.html?id=GTM-WL9SX8HD"
                  height="0" width="0" style="display:none;visibility:hidden"></iframe>
        `,
        body: true,
      },
    ],
    __dangerouslyDisableSanitizersByTagID: {
      "gtag-consent": ["innerHTML"],
      "gtm-loader": ["innerHTML"],
    },
  });
}
</script>
