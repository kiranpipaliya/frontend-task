<script lang="ts" setup>
import navItems from '@/navigation/vertical'
import { themeConfig } from '@themeConfig'
import { useRoute } from 'vue-router'
import { useTheme } from 'vuetify'

// Components
import Footer from '@/layouts/components/Footer.vue'
import UserProfile from '@/layouts/components/UserProfile.vue'
import NavBarI18n from '@core/components/I18n.vue'
import avatar1 from '@images/avatars/avatar-1.png'
import homeSharp from '@images/svg/home-sharp.svg'

// @layouts plugin
import { VerticalNavLayout } from '@layouts'

const route = useRoute()
const vuetifyTheme = useTheme()

// Card background from active theme so it updates in dark mode
const sidebarCardBg = computed(() => {
    const colors = vuetifyTheme.current.value.colors as Record<string, string>
    return colors['grey-light-bg']
})

const logoutBg = computed(() => {
    const colors = vuetifyTheme.current.value.colors as Record<string, string>
    return colors['sidebar-logo-bg']
})

const breadcrumbTitle = computed(() => {
    const name = route.name as string | undefined

    if (name === 'campaign-advanced')
        return 'Advance Campaign'

    if (name === 'campaign-standard')
        return 'Standard Workflow'

    return ''
})

const isRootPage = computed(() => (route.name as string | undefined) === 'root')

const logoutColor = computed(() => {
    const colors = vuetifyTheme.current.value.colors as Record<string, string>
    return colors['sidebar-logo-text']
})
</script>

<template>
    <VerticalNavLayout :nav-items="navItems">
        <!-- 👉 navbar -->
        <template #navbar="{ toggleVerticalOverlayNavActive }">
            <div class="d-flex h-100 align-center justify-space-between navbar-shell">
                <div class="d-flex align-center">
                    <IconBtn id="vertical-nav-toggle-btn" class="ms-n3 d-lg-none"
                        @click="toggleVerticalOverlayNavActive(true)">
                        <VIcon size="26" icon="tabler-menu-2" />
                    </IconBtn>

                    <div class="breadcrumb-bar d-none d-md-flex">
                        <template v-if="isRootPage">
                            <div class="breadcrumb-home-icon">
                                <VIcon :icon="homeSharp" size="16" />
                            </div>
                            <VIcon class="breadcrumb-separator" icon="tabler-chevron-right" size="16" />
                            <span class="breadcrumb-title">Campaign</span>
                        </template>
                        <template v-else>
                            <RouterLink :to="{ name: 'root' }" class="breadcrumb-title breadcrumb-link">
                                Campaign
                            </RouterLink>
                            <template v-if="breadcrumbTitle">
                                <VIcon class="breadcrumb-separator" icon="tabler-chevron-right" size="16" />
                                <span class="breadcrumb-title">
                                    {{ breadcrumbTitle }}
                                </span>
                            </template>
                        </template>
                    </div>
                </div>

                <div class="d-flex align-center gap-x-4">
                    <NavBarI18n v-if="themeConfig.app.i18n.enable && themeConfig.app.i18n.langConfig?.length"
                        :languages="themeConfig.app.i18n.langConfig" />
                    <UserProfile />
                </div>
            </div>
        </template>

        <!-- 👉 sidebar bottom user card -->
        <template #after-vertical-nav-items>
            <div class="px-4 pb-4 mt-auto">
                <VCard class="sidebar-user-card" elevation="0" :style="{ backgroundColor: sidebarCardBg }">

                    <VCardText>
                        <div class="sidebar-user-card-content space-between">
                            <div class="sidebar-user-card-content">
                                <div class="sidebar-avatar-wrapper">
                                    <VAvatar color="primary" size="40" class="*:sidebar-avatar rounded-full">
                                        <VImg :src="avatar1" />
                                    </VAvatar>
                                    <span class="absolute bottom-0 right-0 sidebar-avatar-status" />
                                </div>

                                <div class="flex-grow-1">
                                    <div class="text-body-1 font-weight-medium">
                                        John Doe
                                    </div>
                                    <div class="text-caption text-medium-emphasis">
                                        Admin
                                    </div>
                                </div>
                            </div>


                            <VBtn icon variant="text" :style="{ backgroundColor: logoutBg }" class="logo-button">
                                <VIcon :style="{ color: logoutColor }" icon="tabler-logout" />
                            </VBtn>
                        </div>
                        <div class="text-caption text-medium-emphasis mt-3">
                            Email
                        </div>
                        <div class="text-caption text-medium-emphasis mt-1">
                            johndoe@gmail.com
                        </div>
                    </VCardText>


                </VCard>

                <div class="switch-theme-container">
                    <VCardActions class="switch-theme-content">
                        <ThemeToggleSlider :show-label="false" />
                    </VCardActions>
                </div>
            </div>
        </template>

        <!-- 👉 Pages -->
        <slot />

        <!-- 👉 Footer -->
        <template #footer>
            <Footer />
        </template>

        <!-- 👉 Customizer -->
        <!-- <TheCustomizer /> -->
    </VerticalNavLayout>
</template>

<style scoped>
.navbar-shell {
    padding-inline: 0;
}

.breadcrumb-bar {
    display: inline-flex;
    align-items: center;
    gap: 10px;
}

.breadcrumb-home-icon {

    padding: 0;
    color: rgb(var(--v-theme-primary));
}

.breadcrumb-separator {
    font-size: 0.9rem;
    color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));
}

.breadcrumb-title {
    font-size: 0.9rem;
    font-weight: 500;
    color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));
}

.breadcrumb-link {
    cursor: pointer;
    text-decoration: none;
    color: rgb(var(--v-theme-primary));
}

.sidebar-user-card {
    border-radius: 18px;
}

.sidebar-user-card :deep(.v-card-text) {
    padding: 13px 12px;
}

.sidebar-user-card :deep(.v-card-actions) {
    padding: 8px 18px 16px;
}

.sidebar-avatar {
    position: relative;
}

.sidebar-avatar-wrapper {
    position: relative;
}

.sidebar-avatar-status {
    position: absolute;
    inset-block-end: 0;
    inset-inline-end: 0;
    inline-size: 11px;
    block-size: 11px;
    border-radius: 50%;
    background-color: #28c76f;
    border: 2px solid #f4f5fb;
}

.sidebar-user-card-content {
    display: flex;
    align-items: center;
    gap: 11px;
}

.space-between {
    justify-content: space-between;
}

.switch-theme-container {
    margin-top: 9px;
}

.switch-theme-content {
    padding: 0;
}

.logo-button {
    padding: 6px;
    border-radius: 4px;
}
</style>
