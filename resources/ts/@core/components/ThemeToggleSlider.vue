<script setup lang="ts">
import { useConfigStore } from '@core/stores/config';
import { useTheme } from 'vuetify';

withDefaults(
    defineProps<{
        showLabel?: boolean
    }>(),
    { showLabel: true },
)

const configStore = useConfigStore()
const vuetifyTheme = useTheme()

const isDark = computed({
    get: () => {
        const t = configStore.theme
        if (t === 'dark') return true
        if (t === 'light') return false
        return (vuetifyTheme.global.name.value as string) === 'dark'
    },
    set: (value: boolean) => {
        configStore.theme = value ? 'dark' : 'light'
    },
})

function setLight() {
    isDark.value = false
}

function setDark() {
    isDark.value = true
}

const trackBg = computed(() => {
    const colors = vuetifyTheme.current.value.colors as Record<string, string>
    return colors['grey-200'] ?? '#EEEEEE'
})

const sliderBg = computed(() => {
    const colors = vuetifyTheme.current.value.colors as Record<string, string>
    return colors.surface ?? '#fff'
})

const optionColor = computed(() => {
    const colors = vuetifyTheme.current.value.colors as Record<string, string>
    return colors['on-surface'] ?? '#2F2B3D'
})
</script>

<template>
    <div class="theme-switch-wrapper">
        <div class="theme-toggle-pill" :style="{
            '--theme-track-bg': trackBg,
            '--theme-slider-bg': sliderBg,
            '--theme-option-color': optionColor,
        }">
            <div class="theme-toggle-slider" :class="{ 'is-dark': isDark }" />
            <button type="button" class="theme-toggle-option" :class="{ active: !isDark }" @click="setLight">
                <VIcon icon="tabler-sun" size="18" class="option-icon" />
                <span>Light</span>
            </button>
            <button type="button" class="theme-toggle-option" :class="{ active: isDark }" @click="setDark">
                <VIcon icon="tabler-moon" size="18" class="option-icon" />
                <span>Dark</span>
            </button>
        </div>
        <span v-if="showLabel" class="theme-switch-label">{{ isDark ? 'Dark Mode' : 'Light Mode' }}</span>
    </div>
</template>

<style scoped>
.theme-switch-wrapper {
    display: flex;
    align-items: center;
    gap: 10px;
    width: 100%;
}

.theme-toggle-pill {
    position: relative;
    display: inline-flex;
    align-items: center;
    height: 40px;
    min-width: 180px;
    border-radius: 999px;
    width: 100%;
    background-color: var(--theme-track-bg);
    padding: 4px;
}

.theme-toggle-slider {
    position: absolute;
    top: 2px;
    left: 2px;
    width: calc(50% - 2px);
    height: calc(100% - 4px);
    border-radius: 999px;
    background-color: var(--theme-slider-bg);
    transition: transform 0.3s ease;
    transform: translateX(0);
    pointer-events: none;
}

.theme-toggle-slider.is-dark {
    transform: translateX(calc(100% + 2px));
}

.theme-toggle-option {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 6px;
    position: relative;
    z-index: 1;
    height: 100%;
    min-width: 0;
    padding: 0 16px;
    border: none;
    border-radius: 999px;
    background: transparent;
    color: var(--theme-option-color);
    font-size: 0.875rem;
    font-weight: 500;
    cursor: pointer;
    transition: color 0.2s;
}

.theme-toggle-option .option-icon {
    flex-shrink: 0;
}

.theme-toggle-option:hover {
    opacity: 0.9;
}

.theme-switch-label {
    font-size: 0.875rem;
    color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));
}
</style>
