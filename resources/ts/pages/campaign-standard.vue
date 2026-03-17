<template>
    <div class="flow-page">
        <VCard elevation="0" class="flow-card">
            <div class="flow-frame">
                <!-- Step stepper: Leads List (active), Target Audience, Campaign -->
                <div class="flow-steps">
                    <div class="flow-step-wrapper">
                        <button type="button" class="flow-step"
                            :class="{ 'flow-step--active': activeStep === 'leads', 'flow-step--completed': activeStep === 'audience' || activeStep === 'campaign' }"
                            @click="activeStep = 'leads'">
                            <div class="icon-wrapper"
                                :class="{ 'flow-step--active': activeStep === 'leads', 'flow-step--completed': activeStep === 'audience' || activeStep === 'campaign' }">
                                <VIcon :icon="CheckListIcon" size="18" class="flow-step-icon" />
                            </div>
                            <span class="flow-step-label">Leads List</span>
                        </button>

                        <VIcon class="flow-step-chevron" icon="tabler-chevron-right" size="18" />

                        <button type="button" class="flow-step"
                            :class="{ 'flow-step--active': activeStep === 'audience', 'flow-step--completed': activeStep === 'campaign', 'flow-step--inactive': activeStep === 'leads' }"
                            @click="activeStep = 'audience'">
                            <div class="icon-wrapper"
                                :class="{ 'flow-step--active': activeStep === 'audience', 'flow-step--completed': activeStep === 'campaign', 'flow-step--inactive': activeStep === 'leads' }">
                                <VIcon :icon="NavigationIcon" size="18" class="flow-step-icon" />
                            </div>
                            <span class="flow-step-label">Target Audience</span>
                        </button>

                        <VIcon class="flow-step-chevron" icon="tabler-chevron-right" size="18" />

                        <button type="button" class="flow-step"
                            :class="{ 'flow-step--active': activeStep === 'campaign', 'flow-step--inactive': activeStep !== 'campaign' }"
                            @click="activeStep = 'campaign'">
                            <div class="icon-wrapper"
                                :class="{ 'flow-step--active': activeStep === 'campaign', 'flow-step--inactive': activeStep !== 'campaign' }">
                                <VIcon :icon="MegaphoneIcon" size="18" class="flow-step-icon" />
                            </div>
                            <span class="flow-step-label">Campaign</span>
                        </button>
                    </div>
                </div>

                <!-- Step 1: Leads List – form -->
                <div v-if="activeStep === 'leads'" class="flow-step-pane flow-step-pane--form">
                    <div class="flow-form flow-form-grid">
                        <div class="flow-field flow-field--half">
                            <label class="flow-label">Campaign Name</label>
                            <VTextField v-model="campaignName" variant="outlined" density="compact"
                                placeholder="e.g. Founder Outreach - India" hide-details class="flow-input" />
                            <p class="flow-hint">Give your workflow a clear name so you can find it later.</p>
                        </div>

                        <div class="flow-field flow-field--half">
                            <label class="flow-label">Campaign Type</label>
                            <VSelect v-model="campaignType" :items="campaignTypeItems" variant="outlined"
                                density="compact" hide-details class="flow-input" />
                            <p class="flow-hint">Choose how this workflow will start.</p>
                        </div>

                        <div class="flow-field flow-field--half">
                            <label class="flow-label">Outreach Channel</label>
                            <div class="flow-channel-toggle">
                                <VBtn v-for="opt in outreachChannelOptions" :key="opt.value"
                                    :variant="outreachChannel === opt.value ? 'flat' : 'outlined'"
                                    :color="outreachChannel === opt.value ? 'primary' : undefined" size="small"
                                    class="flow-channel-btn"
                                    :class="{ 'flow-channel-btn--active': outreachChannel === opt.value }"
                                    @click="outreachChannel = opt.value">
                                    {{ opt.label }}
                                </VBtn>
                            </div>
                            <p class="flow-hint">Select how you want to reach your leads in this workflow.</p>
                        </div>

                        <div class="flow-field flow-field--half">
                            <label class="flow-label">Sending Schedule</label>
                            <div class="flow-schedule-row">
                                <div class="flow-schedule-display">
                                    <span class="flow-schedule-text">{{ sendingScheduleLabel }}</span>
                                    <VRadioGroup v-model="selectedScheduleId" hide-details class="flow-schedule-radio"
                                        inline>
                                        <VRadio value="default" />
                                    </VRadioGroup>
                                </div>
                                <VBtn variant="outlined" size="small" class="flow-add-time-btn">
                                    + Add new time
                                </VBtn>
                            </div>
                            <p class="flow-hint">Messages are sent only within your selected hours.</p>
                        </div>
                    </div>

                </div>

                <!-- Step 2: Target Audience – basic LinkedIn / CSV / advanced options -->
                <div v-else-if="activeStep === 'audience'" class="flow-step-pane flow-step-pane--form">
                    <div class="flow-form flow-form-grid-secondary">
                        <div class="audience-card audience-card--wide">
                            <h3 class="audience-card-title">Basic LinkedIn Search</h3>
                            <VTextField variant="outlined" density="compact"
                                placeholder="https://www.linkedin.com/search/results/people/…" hide-details
                                class="flow-input audience-url-input" />
                            <p class="flow-hint audience-hint">
                                Filter profiles in the
                                <a href="https://www.linkedin.com/search/results/people/" target="_blank"
                                    rel="noopener noreferrer" class="audience-link">
                                    LinkedIn search
                                </a>
                                and paste the URL above.
                            </p>
                        </div>

                        <div class="audience-card audience-card--narrow">
                            <h3 class="audience-card-title">Upload CSV File</h3>
                            <!-- <div class="audience-dropzone">
                                <VIcon icon="tabler-upload" size="20" class="audience-dropzone-icon" />
                                <span class="audience-dropzone-label">Drop file here</span>
                            </div> -->
                            <div class="csv-dropzone" @click="csvFileInputRef?.click()" @dragover.prevent
                                @drop.prevent="onCsvDrop">
                                <div>
                                    <div class="csv-dropzone-icon-wrap">
                                        <VIcon icon="tabler-upload" size="19" class="csv-dropzone-icon" />
                                    </div>
                                    <div class="csv-dropzone-label">
                                        Drop file here
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="audience-card audience-card--narrow audience-card--muted">
                            <h3 class="audience-card-title">Advanced options</h3>
                            <div class="audience-advanced-row">
                                <div class="audience-advanced-checkbox-wrap">
                                    <VCheckbox density="compact" hide-details class="audience-advanced-checkbox" />
                                    <div class="audience-advanced-text">
                                        <span class="audience-advanced-label">Use Webhook</span>
                                    </div>
                                </div>

                                <p class="audience-advanced-hint">Use a webhook to send leads automatically.</p>
                            </div>
                        </div>
                    </div>
                </div>
                <!-- Step 3: Campaign – sequence builder (timeline) -->
                <div v-else class="flow-step-pane flow-step-pane--form">
                    <div class="campaign-flow">
                        <div class="campaign-steps">

                            <div class="campaign-step-card-wrap progress-bar campaign-step-card--start">
                                <div class="campaign-step-card">
                                    <div class="progress-bar-inner">
                                        <div class="progress-bar-inner-fill">
                                            <VIcon :icon="GreenCheckIcon" size="14" class="progress-bar-check-icon" />
                                        </div>
                                    </div>
                                    <div class="campaign-step-header">
                                        <div class="campaign-step-header-icon-wrap">
                                            <VIcon :icon="RocketIcon" size="18" class="campaign-step-icon-header" />
                                            <span class="campaign-step-title">Campaign Start</span>
                                        </div>

                                    </div>
                                    <p class="campaign-step-subtitle">
                                        When a lead enters your target audience
                                    </p>
                                </div>
                            </div>

                            <div class="campaign-step-card-wrap progress-bar">
                                <div class="campaign-step-card">
                                    <div class="progress-bar-inner">
                                        <div class="progress-bar-inner-fill">
                                            <VIcon :icon="GreenCheckIcon" size="14" class="progress-bar-check-icon" />
                                        </div>
                                    </div>
                                    <div class="campaign-step-header">
                                        <div class="campaign-step-header-icon-wrap">
                                            <VIcon :icon="LinkedInIcon" size="18" class="campaign-step-icon-header" />
                                            <span class="campaign-step-title">Send LinkedIn Connection Request</span>
                                        </div>
                                        <div class="campaign-step-header-actions">
                                            <button type="button" class="campaign-icon-btn">
                                                <VIcon :icon="PencilEditIcon" size="18" />
                                            </button>
                                            <button type="button" class="campaign-icon-btn campaign-icon-btn--danger">
                                                <VIcon :icon="DeleteIcon" size="18" />
                                            </button>
                                        </div>
                                    </div>
                                    <div class="campaign-step-body">
                                        <div class="campaign-message-preview">
                                            Hi {{ '{' }}{first_name}{{ '}' }}...
                                        </div>
                                        <div class="campaign-step-actions">
                                            <VBtn color="primary" variant="flat" class="campaign-step-btn">
                                                Edit Message
                                            </VBtn>
                                            <VBtn variant="outlined" class="campaign-step-btn campaign-step-btn--ghost">
                                                <VIcon :icon="magicWand" size="18" class="campaign-step-icon-header" />
                                                Make with AI
                                            </VBtn>
                                        </div>
                                    </div>
                                </div>
                            </div>

                            <div class="campaign-step-card-wrap progress-bar">

                                <div class="campaign-step-card">
                                    <div class="progress-bar-inner">
                                        <div class="progress-bar-inner-fill">
                                            <VIcon :icon="GreenCheckIcon" size="14" class="progress-bar-check-icon" />
                                        </div>
                                    </div>
                                    <div class="campaign-step-header">
                                        <div class="campaign-step-header-icon-wrap">
                                            <VIcon :icon="linkForward" size="18" class="campaign-step-icon-header" />
                                            <span class="campaign-step-title">Set Follow-up message</span>
                                        </div>
                                        <div class="campaign-step-header-actions">
                                            <button type="button" class="campaign-icon-btn">
                                                <VIcon :icon="PencilEditIcon" size="18" />
                                            </button>
                                            <button type="button" class="campaign-icon-btn campaign-icon-btn--danger">
                                                <VIcon :icon="DeleteIcon" size="18" />
                                            </button>
                                        </div>
                                    </div>
                                    <div class="campaign-step-body">
                                        <div class="campaign-message-preview">
                                            Hi {{ '{' }}{first_name}{{ '}' }}...
                                        </div>
                                        <div class="campaign-step-actions">
                                            <VBtn color="primary" variant="flat" class="campaign-step-btn">
                                                Edit Message
                                            </VBtn>
                                            <VBtn variant="outlined" class="campaign-step-btn campaign-step-btn--ghost">
                                                <VIcon :icon="magicWand" size="18" class="campaign-step-icon-header" />
                                                Make with AI
                                            </VBtn>
                                        </div>

                                    </div>
                                    <div class="campaign-delay-strip">
                                        <span class="campaign-delay-strip-label">Once accepted wait</span>
                                        <span class="campaign-delay-strip-badge">3</span>
                                        <span class="campaign-delay-strip-unit">Minutes</span>
                                        <span class="campaign-delay-strip-badge">3</span>
                                        <span class="campaign-delay-strip-unit">Hour</span>
                                        <span class="campaign-delay-strip-badge">3</span>
                                        <span class="campaign-delay-strip-unit">days</span>
                                    </div>
                                </div>
                            </div>
                            <div class="campaign-step-card-wrap progress-bar">

                                <div class="campaign-step-card dashed-border">
                                    <div class="progress-bar-inner">
                                        <div class="progress-bar-inner-fill">
                                            <VIcon :icon="GreenCheckIcon" size="14" class="progress-bar-check-icon" />
                                        </div>
                                    </div>
                                    <div class="campaign-step-header mb-0 ">
                                        <div class="campaign-step-header-icon-wrap">
                                            <VIcon :icon="addCircle" size="18" class="campaign-step-icon-header" />
                                            <button type="button" class="campaign-step-title campaign-add-followup">
                                                Add new follow-up
                                            </button>
                                        </div>
                                    </div>
                                </div>
                            </div>


                            <div class="campaign-step-card-wrap progress-bar campaign-step-card--end">

                                <div class="campaign-step-card">
                                    <div class="progress-bar-inner">
                                        <div class="progress-bar-inner-fill">
                                            <VIcon :icon="GreenCheckIcon" size="14" class="progress-bar-check-icon" />
                                        </div>
                                    </div>
                                    <div class="campaign-step-header mb-0 ">
                                        <div class="campaign-step-header-icon-wrap">
                                            <VIcon :icon="minusSignSquare" size="18"
                                                class="campaign-step-icon-header" />
                                            <span class="campaign-step-title">End of Campaign</span>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>


                    </div>
                </div>
                <!-- Footer: Continue at bottom -->
                <div class="flow-footer">
                    <div class="flow-footer-left">
                        <button v-if="activeStep === 'audience' || activeStep === 'campaign'" type="button"
                            class="flow-back-link" @click="goBack">
                            Back
                        </button>
                    </div>

                    <div class="flow-footer-right">
                        <template v-if="activeStep === 'audience'">
                            <VBtn variant="outlined" class="flow-draft-btn" :disabled="true">
                                Save as Draft
                            </VBtn>
                            <VBtn color="primary" class="flow-continue-btn" @click="onContinue">
                                Continue
                            </VBtn>
                        </template>

                        <template v-else-if="activeStep === 'leads'">
                            <VBtn color="primary" class="flow-continue-btn" @click="onContinue">
                                Continue
                            </VBtn>
                        </template>

                        <template v-else>
                            <VBtn variant="outlined" class="flow-draft-btn" :disabled="true">
                                Save as Draft
                            </VBtn>
                            <VBtn color="primary" class="flow-continue-btn" @click="onContinue">
                                Launch Campaign
                            </VBtn>
                        </template>
                    </div>
                </div>

            </div>
        </VCard>
    </div>
</template>

<script setup lang="ts">
import addCircle from '@images/svg/add-circle.svg'
import CheckListIcon from '@images/svg/check-list.svg'
import DeleteIcon from '@images/svg/delete-02.svg'
import GreenCheckIcon from '@images/svg/green-check.svg'
import linkForward from '@images/svg/link-forward.svg'
import LinkedInIcon from '@images/svg/linkedin-01.svg'
import magicWand from '@images/svg/magic-wand-01.svg'
import MegaphoneIcon from '@images/svg/megaphone-02.svg'
import minusSignSquare from '@images/svg/minus-sign-square.svg'
import NavigationIcon from '@images/svg/navigation-03.svg'
import PencilEditIcon from '@images/svg/pencil-edit-02.svg'
import RocketIcon from '@images/svg/rocket-02.svg'
import { computed, ref } from 'vue'
type StandardStep = 'leads' | 'audience' | 'campaign'
const activeStep = ref<StandardStep>('leads')
const campaignName = ref('')
const campaignType = ref('LinkedIn Invitation')
const campaignTypeItems = ['LinkedIn Invitation', 'Email', 'LinkedIn + Email']
const outreachChannel = ref<'email' | 'linkedin' | 'both'>('linkedin')
const outreachChannelOptions = [
    { value: 'email' as const, label: 'Email Only' },
    { value: 'linkedin' as const, label: 'LinkedIn Only' },
    { value: 'both' as const, label: 'LinkedIn + Email' },
]
const selectedScheduleId = ref('default')
const sendingScheduleLabel = computed(() => 'Mon - Fri 9:00 AM - 5:00 PM')

const csvFileInputRef = ref<HTMLInputElement | null>(null)
const csvFileName = ref<string | null>(null)

function onCsvFileSelected(event: Event) {
    const input = event.target as HTMLInputElement | null
    const file = input?.files?.[0]
    csvFileName.value = file ? file.name : null
}

function onCsvDrop(e: DragEvent) {
    const file = e.dataTransfer?.files?.[0]
    if (!file)
        return
    csvFileName.value = file.name
}

function onContinue() {
    if (activeStep.value === 'leads') activeStep.value = 'audience'
    else if (activeStep.value === 'audience') activeStep.value = 'campaign'
}

function goBack() {
    if (activeStep.value === 'audience') activeStep.value = 'leads'
    else if (activeStep.value === 'campaign') activeStep.value = 'audience'
}
</script>

<style scoped>
.flow-page {
    padding: 21px 0;
    height: 100%;
    min-height: 88vh;
    display: flex;
}

.flow-card {
    border-radius: 8px;
    padding: 23px 25px;
    flex: 1;
    display: flex;
    flex-direction: column;
}

.flow-frame {
    border-radius: 12px;
    background-color: rgb(var(--v-theme-surface));
    display: flex;
    flex-direction: column;
    min-height: 0;
}

/* Step stepper – reuse advance flow pattern */
.flow-steps {
    border-radius: 10px;
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
    background-color: rgb(var(--v-theme-surface));
    padding: 4px;
    margin-bottom: 16px;
}

.flow-step-wrapper {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 6px;
    max-width: 954px;
    width: 100%;
}

.flow-step {
    padding: 9px 16px;
    font-size: 13px;
    font-weight: 500;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    border-radius: 8px;
    border: none;
    background-color: transparent;
    cursor: pointer;
}

.icon-wrapper {
    padding: 10px;
    border-radius: 5px;
    background-color: rgb(var(--v-theme-workflow-step-icon-bg));
}

.flow-step--active.icon-wrapper {
    background-color: rgb(var(--v-theme-primary)) !important;
    color: rgb(var(--v-theme-on-primary)) !important;
}

.flow-step--completed.icon-wrapper {
    background-color: rgba(var(--v-theme-primary), 0.12) !important;
    color: rgb(var(--v-theme-primary)) !important;
}

.flow-step--completed.icon-wrapper .flow-step-icon {
    color: rgb(var(--v-theme-primary)) !important;
}

.flow-step--inactive .icon-wrapper,
.flow-step--inactive.icon-wrapper {
    background-color: rgb(var(--v-theme-workflow-step-icon-bg));
    color: rgba(var(--v-theme-workflow-step-label), 0.7);
}

.flow-step--inactive .flow-step-label {
    color: rgba(var(--v-theme-workflow-step-label), 0.7);
}

.flow-step-icon {
    flex-shrink: 0;
    width: 18px;
    height: 18px;
}

.flow-step-chevron {
    color: rgba(var(--v-theme-workflow-title), 1);
}

.flow-step-label {
    white-space: nowrap;
    font-size: 14px;
    font-weight: 500;
    line-height: 130%;
    color: rgb(var(--v-theme-workflow-step-label));
}

.flow-step-pane {
    margin-top: 6px;
    padding-left: 26px;
    flex: 1;
    min-height: 0;
}

.flow-step-pane--form {
    padding-left: 0;
    margin-top: 14px;
}

/* Campaign step (step 3) – timeline and cards */
.campaign-flow {
    display: flex;
    gap: 16px;
    padding-left: 53px;
}

/* Left timeline rail – reuse advanced flow styles */
.campaign-timeline {
    position: relative;
    width: 20px;
}

.campaign-steps {
    flex: 1;
    display: flex;
    flex-direction: column;
    padding-bottom: 22px;
}


.campaign-step-card-wrap {
    padding-bottom: 22px;
}

.campaign-step-card {
    border-radius: 5px;
    border: 1px solid rgb(var(--v-theme-workflow-border-subtle));
    background-color: rgb(var(--v-theme-surface));
    padding: 16px 25px;
    overflow: hidden;
}

.campaign-step-card--end {
    padding-bottom: 0 !important;
}

.campaign-step-card--end .campaign-step-card {
    background-color: rgb(var(--v-theme-workflow-header-bg));
}

.campaign-step-card.dashed-border {
    border-style: dashed !important;
}

.campaign-step-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 6px;
    gap: 8px;
}

.campaign-step-header-icon-wrap {
    display: flex;
    align-items: center;
    gap: 8px;
}

.campaign-step-title {
    font-size: 16px;
    font-weight: 600;
    line-height: 100%;
    color: rgb(var(--v-theme-workflow-text-main));
}

.campaign-step-subtitle {
    font-size: 12px;
    color: rgb(var(--v-theme-workflow-text-muted));
    background-color: rgba(var(--v-theme-primary), 0.04);
    padding: 7px 24px;
    margin: 14px -25px -16px -25px;

}

.campaign-step-icon-header {
    flex-shrink: 0;
    color: rgb(var(--v-theme-primary));
}

.campaign-step-body {
    margin-top: 15px;
    padding: 8px 16px 16px 16px;
    border: 1px solid rgb(var(--v-theme-workflow-border-subtle));
    border-radius: 5px;
}

.campaign-message-preview {
    font-size: 13px;
    color: rgb(var(--v-theme-workflow-text-secondary));
    font-weight: 400;
    line-height: 100%;
    margin-bottom: 43px;
}

.campaign-step-actions {
    display: flex;
    gap: 10px;
}

.campaign-step-btn {
    padding: 10px 22px;
    font-weight: 500;
    background-image: linear-gradient(90deg, rgb(var(--v-theme-primary)) 0%, rgb(var(--v-theme-primary-gradient-end)) 100%);
    color: rgb(var(--v-theme-on-primary)) !important;
    border-radius: 5px;
    line-height: 100%;
}

.campaign-step-btn--ghost {
    border-color: rgb(var(--v-theme-primary));
    color: rgb(var(--v-theme-primary)) !important;
    background: rgb(var(--v-theme-surface));
    display: inline-flex;
    align-items: center;
    gap: 10px;
}

.campaign-step-btn--ghost :deep(.v-btn__content) {
    gap: 10px !important;
}

.campaign-delay-row {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    gap: 8px;
    margin-top: 10px;
}

.campaign-delay-label {
    font-size: 12px;
    color: rgb(var(--v-theme-workflow-text-muted));
}

.campaign-delay-chip {
    text-transform: none;
    font-size: 12px;
}

.campaign-delay-chip :deep(.v-chip__content) {
    padding-inline: 8px;
}

.campaign-delay-strip {
    margin-top: 9px;
    padding: 16px 18px;
    border-radius: 5px;
    border: 1px dashed rgb(var(--v-theme-workflow-border-dashed));
    display: flex;
    align-items: center;
    gap: 5px;
}

.campaign-delay-strip-label {
    font-size: 14px;
    color: rgb(var(--v-theme-workflow-text-secondary));
    line-height: 100%;
    font-weight: 400;
}

.campaign-delay-strip-badge {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 2px 11px;
    border-radius: 5px;
    border: 1px solid rgb(var(--v-theme-workflow-border-subtle));
    font-size: 14px;
    font-weight: 400;
    line-height: 100%;
    color: rgb(var(--v-theme-workflow-text-secondary));
    background-color: rgb(var(--v-theme-surface));
}

.campaign-delay-strip-unit {
    font-size: 12px;
    color: rgb(var(--v-theme-workflow-text-main));
}

.campaign-step-card--add {
    background-color: transparent;
    border-style: dashed;
}

.campaign-add-followup {
    text-align: left;
}

.mb-0 {
    margin-bottom: 0 !important;
}

.campaign-step-header-actions {
    display: flex;
    align-items: center;
    gap: 10px;
}

.campaign-icon-btn {
    border: none;
    background: transparent;
    padding: 0;
    margin: 0;
    cursor: pointer;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    color: rgb(var(--v-theme-sidebar-logo-text));
}

.campaign-icon-btn--danger {
    color: rgb(var(--v-theme-error));
}

/* Form – labels, inputs, hints */
.flow-form {
    padding: 0 23px;
}

.flow-form-grid {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    column-gap: 24px;
    row-gap: 44px;

}

.flow-field--half {
    margin-bottom: 0;
}

.flow-field {
    margin-bottom: 0;
}

.flow-form-grid-secondary {
    display: grid;
    grid-template-columns: 3.16fr 1.1fr 1.1fr;
    gap: 16px;
    align-items: stretch;
    padding: 0;
    padding-left: 9px;
    margin-bottom: 26px;
}

.flow-label {
    display: block;
    font-size: 14px;
    font-weight: 500;
    color: rgb(var(--v-theme-workflow-text-main));
    line-height: 23px;
    margin-bottom: 12px;
}

.flow-input {
    font-size: 14px;
}

.flow-input :deep(.v-field__input) {
    font-size: 14px !important;
    font-weight: 400 !important;
    line-height: 24px !important;
    padding: 8px 53px 8px 15px !important;
    color: rgb(var(--v-theme-on-surface)) !important;
}

.flow-input :deep(.v-field) {
    border-radius: 8px;
    border-color: rgba(var(--v-theme-grey-300), 0.9);
    background-color: rgb(var(--v-theme-surface));
    box-shadow: none;
}

.flow-input :deep(.v-field__input::placeholder) {
    color: rgba(var(--v-theme-on-surface), 0.45);
}

.flow-input :deep(.v-field--focused),
.flow-input :deep(.v-field:hover) {
    border-color: rgba(var(--v-theme-grey-400), 1);
}

.flow-hint {
    font-size: 12px;
    color: rgb(var(--v-theme-workflow-text-muted));
    margin: 6px 0 0;
    font-weight: 400;
    line-height: 18px;
}

@media (max-width: 960px) {
    .flow-form-grid {
        grid-template-columns: 1fr;
    }

    .flow-form-grid-secondary {
        grid-template-columns: 1fr;
    }
}

/* Outreach channel segmented buttons */
.flow-channel-toggle {
    display: flex;
    flex-wrap: nowrap;
    gap: 0;
    border-radius: 5px;
    border: 1px solid rgb(var(--v-theme-workflow-border-strong));
    background-color: rgb(var(--v-theme-surface));
    overflow: hidden;
}

.flow-channel-btn {
    flex: 1 1 0;
    text-transform: none;
    border-radius: 0;
    padding: 13px 30px !important;
    font-size: 14px !important;
    font-weight: 400;
    line-height: 1 !important;
    color: rgb(var(--v-theme-workflow-text-muted)) !important;
    height: 100%;
    display: flex;
    margin: -2px !important;
}

.flow-channel-btn.v-btn--variant-outlined {
    border-color: transparent;
    color: rgb(var(--v-theme-workflow-text-muted));
    background-color: transparent;
}

.flow-channel-btn .v-btn__content {}

.flow-channel-btn:hover,
.flow-channel-btn.flow-channel-btn--active:hover,
.flow-channel-btn.flow-channel-btn--active {
    box-shadow: none !important;
    border-radius: 0px !important;
    border: none !important;
    height: 100%;
}

.flow-channel-btn::after {
    display: none;
}

.flow-channel-btn--active.v-btn--variant-flat,
.flow-channel-btn:hover,
.flow-channel-btn.flow-channel-btn--active:hover,
.flow-channel-btn:focus {
    border: none !important;
    background-color: rgb(var(--v-theme-primary)) !important;
    color: rgb(var(--v-theme-on-primary)) !important;
}

.flow-channel-btn:not(:last-child) {
    border-right: 1px solid rgba(var(--v-theme-grey-300), 0.9);
}

/* Sending schedule row */
.flow-schedule-row {
    display: flex;
    align-items: stretch;
    gap: 11px;

}

/* Target Audience cards (step 2) */
.audience-card {
    border-radius: 5px;
    border: 1px solid rgb(var(--v-theme-workflow-border-strong));
    background-color: rgb(var(--v-theme-surface));
    padding: 20px;
}

.audience-card--wide {
    min-width: 0;
    max-height: 600px;
    width: 100%;
    padding: 20px 25px;
}

.audience-card--narrow {
    min-width: 0;
}



.audience-card-title {
    margin-bottom: 12px;
    font-size: 14px;
    font-weight: 500;
    line-height: 23px;
    color: rgb(var(--v-theme-workflow-text-main));
}

.audience-url-input :deep(.v-field__input) {
    padding: 10px 15px !important;
    font-size: 14px !important;
    font-weight: 400 !important;
    line-height: 100% !important;
}

.audience-hint {
    margin-top: 6px;
    font-size: 12px;
    font-weight: 400;
    line-height: 18px;
    color: rgb(var(--v-theme-workflow-text-muted));

}

.audience-link {
    color: rgb(var(--v-theme-primary));
    text-decoration: underline;
}

.audience-dropzone {
    margin-top: 12px;
    border-radius: 4px;
    border: 1px dashed rgb(var(--v-theme-primary));
    background-color: rgba(55, 98, 238, 0.02);
    padding: 11px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 3px;
}

.csv-dropzone {
    border-radius: 10px;
    border: 2px dashed rgba(var(--v-theme-primary), 0.45);
    padding: 11px;
    text-align: center;
    background-color: rgba(var(--v-theme-primary), 0.04);
    cursor: pointer;
    transition: border-color 0.2s ease, background-color 0.2s ease;
}

.csv-dropzone:hover {
    background-color: rgba(var(--v-theme-primary), 0.07);
    border-color: rgba(var(--v-theme-primary), 0.6);
}

.csv-dropzone-icon-wrap {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    border-radius: 6px;
    background-color: rgba(var(--v-theme-primary), 0.1);
    color: rgb(var(--v-theme-primary));
    padding: 6px;
    margin-bottom: 3px;
}

.csv-dropzone-icon {
    width: 19px;
    height: 19px;
    color: rgb(var(--v-theme-primary));
}

.csv-dropzone-label {
    font-size: 14px;
    font-weight: 400;
    color: rgb(var(--v-theme-primary));
    line-height: 24px;
}

.audience-advanced-row {
    margin-top: -4px;
}

.audience-card--muted .audience-advanced-label {
    color: rgb(var(--v-theme-workflow-text-muted));
    font-size: 14px;
    font-weight: 400;
    line-height: 100%;
}

.audience-advanced-hint {
    font-size: 12px;
    font-weight: 400;
    line-height: 18px;
    color: rgb(var(--v-theme-workflow-text-muted));
    margin-top: 23px;
    margin-bottom: 0;
}

.audience-advanced-checkbox {
    margin-left: -5px;
}

.audience-advanced-checkbox-wrap {
    display: flex;
    align-items: center;
    gap: 1.5px;
}

.audience-advanced-checkbox :deep(.v-selection-control) {
    padding: 0;
    margin: 0;
}

.audience-advanced-checkbox :deep(.v-selection-control__input) {
    color: rgb(var(--v-theme-grey-400));
}

.audience-advanced-checkbox :deep(.v-selection-control--dirty .v-selection-control__input) {
    color: rgb(var(--v-theme-primary));
}

.audience-advanced-label {
    display: block;
    font-size: 13px;
    font-weight: 500;
    color: rgb(var(--v-theme-workflow-text-main));
}



.flow-schedule-display {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 10px;
    padding: 10px 13px;
    border: 1px solid rgb(var(--v-theme-workflow-border-strong));
    background-color: rgb(var(--v-theme-surface));
    border-radius: 5px;
    max-width: 308px;
    width: 100%;
}


.flow-schedule-text {
    font-size: 14px;
    font-weight: 400;
    line-height: 100%;
    color: rgb(var(--v-theme-workflow-text-muted));
}

.flow-schedule-radio {
    margin: 0;
    padding: 0;
}

.flow-schedule-radio :deep(.v-selection-control-group) {
    flex-direction: row;
    justify-content: end;
}

.flow-schedule-radio :deep(.v-selection-control) {
    padding: 0;
    margin: 0;
}

.flow-schedule-radio :deep(.v-selection-control__wrapper) {
    width: 18px;
    height: 18px;
}

.flow-schedule-radio :deep(.v-selection-control__input) {
    color: rgb(var(--v-theme-primary));
}

.flow-add-time-btn {
    text-transform: none;
    font-weight: 400 !important;
    font-size: 14px !important;
    line-height: 100% !important;
    color: rgb(var(--v-theme-workflow-text-muted)) !important;
    border-radius: 5px !important;
    border-color: rgb(var(--v-theme-workflow-border-strong));
    color: rgb(var(--v-theme-workflow-text-muted)) !important;
    padding: 10px 13px !important;
    max-width: 183px;
    height: 38px;
    width: 100%;
}

.flow-placeholder {
    font-size: 14px;
    color: rgba(var(--v-theme-on-surface), 0.7);
}



.progress-bar {
    position: relative;
}

.progress-bar--has-selection .progress-bar-inner {
    background-color: rgba(var(--v-theme-primary), 0.2);
}

.progress-bar--content-filled .progress-bar-inner-fill .progress-bar-check-icon {
    display: inline-flex;
}

.progress-bar-inner {
    position: absolute;
    left: -18px;
    top: 27px;
    bottom: 0;
    width: 3px;
    background-color: rgb(var(--v-theme-workflow-header-bg)) !important;
    z-index: 2;
}

.progress-bar-inner:after {
    content: '';
    position: absolute;
    left: 0;
    top: -57px;
    bottom: 0;
    z-index: 1;
    width: 3px;
    background-color: rgb(var(--v-theme-workflow-header-bg)) !important;
}

.progress-bar-inner-fill {
    width: 14px;
    height: 14px;
    z-index: 3;
    border-radius: 50%;
    border: 2px solid rgb(var(--v-theme-primary));
    background-color: rgb(var(--v-theme-surface));
    position: absolute;
    left: -6px;
    top: -8px;

}

.not-first-progress {
    padding-top: 33px;
}

.not-first-progress .progress-bar-inner {
    top: 0;
}

.not-first-progress .progress-bar-inner-fill {
    top: 50px;
}

.progress-bar-check-icon {
    display: none;
    position: absolute;
    top: -2px;
    left: -2px;
}



/* Footer – button at bottom */
.flow-footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 12px;
    margin-top: auto;
    padding-top: 18px;
    padding-left: 35px;
}

.flow-footer-left {
    min-width: 0;
}

.flow-footer-right {
    display: flex;
    align-items: center;
    gap: 10px;
}

.flow-back-link {
    background: transparent;
    border: none;
    padding: 0;
    margin: 0;
    font-size: 14px;
    font-weight: 500;
    color: rgb(var(--v-theme-primary));
    cursor: pointer;
}

.flow-back-link span {
    margin-right: 4px;
}

.flow-draft-btn {
    text-transform: none;
    font-weight: 500;
    font-size: 14px;
    border-radius: 5px;
    border: none !important;
    line-height: 100%;
    letter-spacing: 0.4px;
    color: rgb(var(--v-theme-workflow-text-muted)) !important;
    background-color: rgb(var(--v-theme-workflow-step-icon-bg)) !important;
}

.flow-continue-btn {
    padding: 10px 22px;
    font-weight: 500;
    background-image: linear-gradient(90deg, rgb(var(--v-theme-primary)) 0%, rgb(var(--v-theme-primary-gradient-end)) 100%);
    color: rgb(var(--v-theme-on-primary)) !important;
    border-radius: 5px;
    line-height: 100%;
}

.flow-prev-btn .flow-prev-btn-icon {
    color: rgb(var(--v-theme-primary)) !important;
    margin-right: 6px;
}



.flow-continue-btn:hover:not(.v-btn--disabled) {
    box-shadow: 0 2px 8px rgba(var(--v-theme-primary), 0.4);
    opacity: 0.95;
}
</style>

<route lang="yaml">
meta:
  navActiveLink: Campaign
</route>
