<template>
    <div class="campaign-page">
        <!-- Top breadcrumb + user summary bar is part of the default layout.
         Here we render the inner card content that matches the Figma. -->

        <VCard elevation="0" class="campaign-card">
            <!-- Filters row -->
            <VRow class="campaign-filters" align="center">
                <VCol cols="12" md="3" class="campaign-filter-select">
                    <VSelect :items="statusOptions" variant="outlined" density="comfortable" hide-details rounded="lg"
                        v-model="selectedStatus" />
                </VCol>

                <VCol cols="12" md="5" class="campaign-filter-search">
                    <VTextField v-model="search" :placeholder="'Search'" prepend-inner-icon="tabler-search"
                        variant="outlined" density="comfortable" hide-details rounded="lg" />
                </VCol>
            </VRow>

            <!-- Empty state illustration + CTA -->
            <div class="campaign-empty-state">
                <img :src="emptyStateImage" alt="No campaigns yet" class="campaign-illustration">

                <VBtn class="campaign-cta" color="primary" rounded="lg" size="large"
                    @click="isWorkflowDialogOpen = true">
                    New Campaign
                </VBtn>
            </div>
        </VCard>

        <!-- Workflow selection modal -->
        <VDialog v-model="isWorkflowDialogOpen" max-width="683" scrim="transparent" class="workflow-dialog">
            <VCard class="workflow-modal-card" elevation="0">
                <div class="workflow-modal-header">
                    <div>
                        <div class="workflow-title text-h6 font-weight-semibold">
                            Select Workflow Mode
                        </div>
                        <div class="workflow-subtitle text-body-2 text-medium-emphasis">
                            Choose how you want your campaign to behave.
                        </div>
                    </div>

                    <VBtn icon variant="text" class="workflow-close-btn " @click="isWorkflowDialogOpen = false">
                        <VIcon icon="tabler-circle-x workflow-close-icon" size="30" />
                    </VBtn>
                </div>

                <VRow class="mb-4 card-content" dense>
                    <VCol cols="12">
                        <VCard class="mode-card" :class="{ 'mode-card--primary': selectedWorkflow === 'advanced' }"
                            elevation="0" @click="selectedWorkflow = 'advanced'">
                            <div class="mode-card-inner">
                                <div class="mode-card-inner-content">
                                    <div class="mode-left">
                                        <div class="mode-radio"
                                            :class="{ 'mode-radio--active': selectedWorkflow === 'advanced' }" />
                                    </div>
                                    <div class="mode-center">
                                        <div class="text-subtitle-1 font-weight-semibold">
                                            Advanced Workflow
                                            <VChip color="success" variant="elevated" size="small" class="mode-chip">
                                                Recommended
                                            </VChip>
                                        </div>
                                        <div class="text-caption text-medium-emphasis mt-1">
                                            Best for high-volume outreach
                                        </div>
                                        <div class="mode-features text-caption text-medium-emphasis mt-2">
                                            <span>Conditional logic</span>
                                            <span>Multiple paths</span>
                                            <span>More control</span>
                                        </div>
                                    </div>
                                </div>
                                <div class="mode-right">
                                    <img :src="advancedFlowImage" alt="Advanced workflow illustration"
                                        class="mode-illustration">
                                </div>
                            </div>
                        </VCard>
                    </VCol>

                    <VCol cols="12">
                        <VCard class="mode-card" :class="{ 'mode-card--primary': selectedWorkflow === 'standard' }"
                            elevation="0" @click="selectedWorkflow = 'standard'">
                            <div class="mode-card-inner">
                                <div class="mode-card-inner-content">
                                    <div class="mode-left">
                                        <div class="mode-radio"
                                            :class="{ 'mode-radio--active': selectedWorkflow === 'standard' }" />
                                    </div>
                                    <div class="mode-center">
                                        <div class="text-subtitle-1 font-weight-semibold">
                                            Standard Workflow
                                        </div>
                                        <div class="text-caption text-medium-emphasis mt-1">
                                            Best for beginners
                                        </div>
                                        <div class="mode-features text-caption text-medium-emphasis mt-2">
                                            <span>Linear steps</span>
                                            <span>No conditions</span>
                                            <span>Easy Setup</span>
                                        </div>
                                    </div>
                                </div>
                                <div class="mode-right">
                                    <img :src="standardFlowImage" alt="Standard workflow illustration"
                                        class="mode-illustration">
                                </div>
                            </div>
                        </VCard>
                    </VCol>
                </VRow>

                <VCardActions class="justify-end card-actions">
                    <VBtn variant="text" class="me-2 footer-close-btn" @click="isWorkflowDialogOpen = false">
                        Close
                    </VBtn>
                    <VBtn color="primary" class="footer-next-btn" @click="handleWorkflowNext">
                        Next
                    </VBtn>
                </VCardActions>
            </VCard>
        </VDialog>
    </div>
</template>

<script setup lang="ts">
import advancedFlowImage from '@images/svg/advance-flow.svg?url'
import emptyStateImage from '@images/svg/empty-state.svg?url'
import standardFlowImage from '@images/svg/standard-flow.svg?url'
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const statusOptions = ['All', 'Active', 'Paused', 'Draft']
const selectedStatus = ref('All')
const search = ref('')

const isWorkflowDialogOpen = ref(false)
const selectedWorkflow = ref<'advanced' | 'standard'>('advanced')

const router = useRouter()
const handleWorkflowNext = () => {
    isWorkflowDialogOpen.value = false

    if (selectedWorkflow.value === 'advanced')
        router.push('/campaign-advanced')
    else
        router.push('/campaign-standard')
}
</script>

<style scoped>
.campaign-page {
    padding: 21px 0;
}

.card-content {
    padding: 28px 48px 0 48px;
    display: flex;
    flex-direction: column;
    gap: 20px;
    margin: 0;
}

.card-actions {
    padding: 16px 48px 20px;
    border-top: 1px solid rgba(var(--v-theme-grey-200), 1);
}

.campaign-card {
    border-radius: 8px;
    padding: 31px 14px;
    height: 88vh;
    display: flex;
    flex-direction: column;
}

.campaign-filters {
    gap: 0.75rem;
    display: flex;
    flex: 0;
    flex-wrap: wrap;
}

@media (min-width: 960px) {
    .campaign-filters {
        justify-content: flex-start;
        gap: 1rem;
        margin-bottom: 1.5rem;
    }

    .campaign-filter-select {
        max-width: 180px;
    }

    .campaign-filter-search {
        max-width: 320px;
    }
}

.campaign-empty-state {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    gap: 1.75rem;
    margin-top: 90px;
    padding-inline: 1rem;
}

.campaign-illustration {
    max-width: min(353px, 80vw);
    width: 100%;
    height: auto;
    min-height: 200px;
    object-fit: contain;
}

.campaign-cta {
    text-transform: none;
    font-weight: 600;
    letter-spacing: 0.03em;
    padding-inline: 2.75rem;
    border-radius: 999px;
    background-image: linear-gradient(90deg, rgb(var(--v-theme-primary)) 0%, rgb(var(--v-theme-primary-gradient-end)) 100%);
    color: rgb(var(--v-theme-on-primary));
    box-shadow: 0 8px 20px rgba(var(--v-theme-primary), 0.35);
    max-width: 100%;
}

.workflow-modal-card {
    border-radius: 18px;
    padding: 0;
    box-shadow: 0 18px 45px rgba(var(--v-theme-workflow-scrim), 0.16);
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
    max-width: 100%;
}

.workflow-dialog {
    background-color: rgba(var(--v-theme-workflow-scrim), 0.06);
    backdrop-filter: blur(4px);
}

.workflow-modal-header {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    margin-bottom: 12px;
    padding: 12px 48px;
    background-color: rgb(var(--v-theme-workflow-header-bg));
    position: relative;
    margin: 0;
}

.workflow-title {
    margin-bottom: 4px;
    font-size: 18px;
    font-weight: 500;
    line-height: 100%;
    color: rgb(var(--v-theme-workflow-title));
    margin-bottom: 3px;
}

.workflow-subtitle {
    max-width: 360px;
    font-size: 14px;
    font-weight: 400;
    line-height: 21px;
    color: rgb(var(--v-theme-workflow-title));
}

.workflow-close-btn {
    margin-top: -4px;
    position: absolute;
    top: 50%;
    right: 15px;
    transform: translateY(-50%);

}

.workflow-close-icon {
    color: rgba(var(--v-theme-grey-600), 1);
}

.mode-card {
    border-radius: 14px;
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
}

.mode-card--primary {
    border-color: rgba(var(--v-theme-primary), 0.24);
    background-color: rgba(var(--v-theme-primary), 0.04);
}

.mode-card-inner {
    display: flex;
    align-items: center;
    padding: 31px 14px;
    gap: 13px;
    justify-content: space-between;
}

.mode-card-inner-content {
    display: flex;
    align-items: flex-start;
    gap: 13px;

}

.mode-center .text-caption {
    font-size: 14px;
    font-weight: 400;
    line-height: 21px;
    color: rgb(var(--v-theme-workflow-title));
}

.mode-center .text-subtitle-1 {
    font-size: 14px;
    font-weight: 700;
    line-height: 21px;
    margin-top: -2px;
    color: rgb(var(--v-theme-workflow-title));
}

.mode-left {
    display: flex;
    align-items: center;
}

.mode-radio {
    width: 20px;
    height: 20px;
    border-radius: 999px;
    border: 2px solid rgba(var(--v-theme-grey-400), 1);
    box-sizing: border-box;
    background-color: rgb(var(--v-theme-surface));
    transition:
        border-color 0.15s ease,
        box-shadow 0.15s ease,
        background-color 0.15s ease;
}

.mode-radio--active {
    border-color: rgba(var(--v-theme-primary), 1);
    box-shadow: 0 0 0 3px rgba(var(--v-theme-primary), 0.24);
    position: relative;
}

.mode-radio--active::after {
    content: '';
    position: absolute;
    inset: 0;
    border-radius: inherit;
    background-color: rgba(var(--v-theme-primary), 1);
}

.mode-radio--active::before {
    content: '';
    position: absolute;
    inset: 4px;
    z-index: 999;
    border-radius: inherit;
    background-color: rgb(var(--v-theme-surface));
}

.mode-center {
    flex: 1;
}

.mode-features {
    display: inline-flex;
    align-items: center;
    gap: 20px;
}

.mode-features span {
    display: inline-flex;
    align-items: center;
    gap: 6px;
}

.mode-features span::before {
    content: '';
    inline-size: 6px;
    block-size: 6px;
    border-radius: 999px;
    background-color: rgba(var(--v-theme-workflow-title), 0.75);
}

.mode-right {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    gap: 8px;
}

.mode-illustration {
    width: 90px;
    height: auto;
    object-fit: contain;
    margin-top: -25px;
}

.mode-chip {
    align-self: flex-start;
    border-radius: 999px !important;
    padding: 0 9px;
    font-size: 12px;
    line-height: 18px;
    font-weight: 500;
    background-color: rgb(var(--v-theme-workflow-chip-bg)) !important;
    color: rgb(var(--v-theme-workflow-chip-text)) !important;
}

.footer-close-btn {
    padding: 10px 22px;
    font-weight: 500;
    background-color: rgb(var(--v-theme-grey-200));
    line-height: 100%;
    font-size: 14px;
    color: rgb(var(--v-theme-workflow-footer-close-text)) !important;
    border-radius: 5px;
}

.footer-next-btn {
    padding: 10px 22px;
    font-weight: 500;
    background-image: linear-gradient(90deg, rgb(var(--v-theme-primary)) 0%, rgb(var(--v-theme-primary-gradient-end)) 100%);
    color: rgb(var(--v-theme-on-primary)) !important;
    border-radius: 5px;
    line-height: 100%;
}

@media (max-width: 959px) {
    .campaign-page {
        padding: 1rem 0;
    }

    .campaign-filters {
        gap: 0;
        flex-wrap: nowrap;
    }

    .campaign-filters .campaign-filter-select,
    .campaign-filters .campaign-filter-search {
        flex: 0 0 200px;
    }

    .campaign-card {
        padding: 1rem 1rem 2rem;
    }

}

@media (max-width: 768px) {
    .workflow-dialog .workflow-modal-header {
        padding: 12px 46px 12px 20px;
    }

    .card-content {
        padding: 16px 20px 0 20px;
    }

    .workflow-dialog .mode-features {
        gap: 5px;
        flex-direction: column;
        align-items: flex-start;
    }

}

@media (max-width: 575px) {
    .campaign-filters {
        flex-wrap: wrap;
    }

    .campaign-filters .campaign-filter-select,
    .campaign-filters .campaign-filter-search {
        flex: 0 0 100%;
        padding-bottom: 0;
    }

    .campaign-empty-state {
        margin-top: 10%;
        gap: 10px;

    }

    .campaign-cta {
        padding: 10px 24px !important;
        height: auto;
        font-size: 14px !important;
    }

    .workflow-dialog :deep(.v-overlay__content) {
        margin: 12px !important;
        width: 300px;
    }

    .workflow-dialog :deep(.v-card-actions) {
        padding: 12px 20px !important;
    }

    .mode-radio {
        width: 16px;
        height: 16px;
    }

    .mode-card-inner {
        padding: 16px 10px;
        flex-direction: column;
        align-items: flex-start;


    }

    .mode-card-inner {
        width: 100%;
        align-items: center;
    }

    .card-content {
        gap: 14px;
    }

}
</style>
