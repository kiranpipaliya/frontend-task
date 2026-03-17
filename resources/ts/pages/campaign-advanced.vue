<template>
    <div class="flow-page">
        <VCard elevation="0" class="flow-card">

            <!-- Main bordered container -->
            <div class="flow-frame">
                <!-- Step tabs row -->
                <div class="flow-steps">
                    <button type="button" class="flow-step" :class="{ 'flow-step--completed': activeStep === 'sender' }"
                        @click="activeStep = 'define'">
                        <div class="icon-wrapper"
                            :class="{ 'flow-step--active': activeStep === 'define', 'flow-step--completed': activeStep === 'sender' }">
                            <VIcon :icon="CheckListIcon" size="18" class="flow-step-icon" />
                        </div>
                        <span class="flow-step-label">
                            Define Target Audience
                        </span>
                    </button>

                    <VIcon class="flow-step-chevron" icon="tabler-chevron-right" size="18" />

                    <button type="button" class="flow-step" @click="activeStep = 'sender'">
                        <div class="icon-wrapper" :class="{ 'flow-step--active': activeStep === 'sender' }">
                            <VIcon :icon="UserSearchIcon" size="18" class="flow-step-icon" />
                        </div>
                        <span class="flow-step-label">
                            Sender Profiles
                        </span>
                    </button>
                </div>

                <!-- Step 1: two accordions - first (method choice), second (content, only after selection) -->
                <div v-if="activeStep === 'define'" class="flow-step-pane">
                    <!-- Accordion 1: Choose Import Method (can close on its own) -->
                    <VExpansionPanels v-model="importPanel" class="progress-bar flow-import-panels no-icon-rotate"
                        :class="{ 'progress-bar--has-selection': selectedImportMethod !== null }">
                        <div class="progress-bar-inner">
                            <div class="progress-bar-inner-fill">
                                <VIcon :icon="GreenCheckIcon" size="14" class="progress-bar-check-icon" />
                            </div>
                        </div>
                        <VExpansionPanel :value="0">
                            <VExpansionPanelTitle class="flow-import-header"
                                :class="{ 'flow-import-header--has-selection': selectedImportMethod !== null }"
                                expand-icon="tabler-chevron-down" collapse-icon="tabler-chevron-up">
                                <span class="flow-import-title">{{ importAccordionTitle }}</span>
                                <VChip v-if="selectedImportMethod !== null" size="small" variant="tonal" color="default"
                                    class="flow-content-step-badge">
                                    Step 1 of 2
                                </VChip>
                            </VExpansionPanelTitle>
                            <VExpansionPanelText class="flow-import-body">
                                <div class="flow-import-row">
                                    <div v-for="card in cards" :key="card.id" class="flow-import-col">
                                        <VCard elevation="0" class="flow-import-card"
                                            :class="{ 'flow-import-card--selected': selectedImportMethod === card.id }"
                                            @click="onImportMethodCardClick(card.id)">
                                            <div v-if="selectedImportMethod === card.id" class="flow-import-card-badge">
                                                <VIcon icon="tabler-check" size="14" />
                                            </div>
                                            <div class="flow-import-card-icon">
                                                <VIcon :icon="card.icon" size="20" />
                                            </div>
                                            <div class="flow-import-card-title">
                                                {{ card.title }}
                                            </div>
                                            <div class="flow-import-card-subtitle">
                                                {{ card.subtitle }}
                                                <a v-if="card.downloadLabel" href="#" class="flow-import-card-link"
                                                    @click.prevent>
                                                    {{ card.downloadLabel }}
                                                </a>
                                            </div>
                                        </VCard>
                                    </div>
                                </div>
                            </VExpansionPanelText>
                        </VExpansionPanel>
                    </VExpansionPanels>

                    <!-- Accordion 2: content for selected method (hidden when Lead Lists selected – use modal instead) -->
                    <div v-if="selectedImportMethod !== null && selectedImportMethod !== 'lookalike'"
                        class="flow-content-accordion">
                        <VExpansionPanels v-model="contentPanel"
                            class="progress-bar not-first-progress flow-content-panels no-icon-rotate"
                            :class="{ 'progress-bar--content-filled': isContentFilled }">
                            <div class="progress-bar-inner">
                                <div class="progress-bar-inner-fill">
                                    <VIcon :icon="GreenCheckIcon" size="14" class="progress-bar-check-icon" />
                                </div>
                            </div>
                            <VExpansionPanel :value="0">
                                <VExpansionPanelTitle class="flow-content-header" :class="[
                                    { 'flow-content-header--filled': isContentFilled },
                                    selectedImportMethod ? `${selectedImportMethod}-search-header` : '',
                                ]" expand-icon="tabler-chevron-down" collapse-icon="tabler-chevron-up">
                                    <span class="flow-import-title">{{ contentAccordionTitle }}</span>
                                    <VChip size="small" variant="tonal" color="default" class="flow-content-step-badge">
                                        Step 1 of 2
                                    </VChip>
                                </VExpansionPanelTitle>
                                <VExpansionPanelText class="flow-content-body"
                                    :class="selectedImportMethod ? `${selectedImportMethod}-search-body` : ''">
                                    <!-- LinkedIn Search -->
                                    <template v-if="selectedImportMethod === 'linkedin'">
                                        <div class="flow-method-header-row">
                                            <div class="flow-method-header">
                                                <div class="flow-method-header-icon">
                                                    <VIcon :icon="LinkedInIcon" size="16" />
                                                </div>
                                                <span class="flow-method-header-text">
                                                    Find your target audience with
                                                    <a href="https://www.linkedin.com/search/results/people/"
                                                        target="_blank" rel="noopener noreferrer" class="flow-link"
                                                        @click.prevent="openInNewTab('https://www.linkedin.com/search/results/people/')">LinkedIn
                                                        Search</a>
                                                    or
                                                    <a href="https://www.linkedin.com/sales/search" target="_blank"
                                                        rel="noopener noreferrer" class="flow-link"
                                                        @click.prevent="openInNewTab('https://www.linkedin.com/sales/search')">Sales
                                                        Navigator</a>
                                                    or
                                                    <a href="https://www.linkedin.com/posts/salestechexpert_technology-prospecting-sales-activity-6863556330968420353-MOrv/?utm_source=linkedin_share&utm_medium=member_desktop_web"
                                                        target="_blank" rel="noopener noreferrer" class="flow-link"
                                                        @click.prevent="openInNewTab('https://www.linkedin.com/posts/salestechexpert_technology-prospecting-sales-activity-6863556330968420353-MOrv/?utm_source=linkedin_share&utm_medium=member_desktop_web')">Post
                                                        URL</a>
                                                    or
                                                    <a href="https://app.linkedfusion.io/web/login" target="_blank"
                                                        rel="noopener noreferrer" class="flow-link"
                                                        @click.prevent="openInNewTab('https://app.linkedfusion.io/web/login')">Group
                                                        URL</a>
                                                </span>
                                            </div>
                                            <a href="#" class="flow-search-guide-link">
                                                <VIcon icon="tabler-info-circle" size="18" class="me-1" />
                                                Search Guide
                                            </a>
                                        </div>
                                        <div class="flow-search-row">
                                            <VTextField v-model="linkedInSearchUrl" variant="outlined"
                                                hide-details="auto" density="comfortable" class="flow-text-field"
                                                placeholder="https://www.linkedin.com/search/results/people/?keywords="
                                                :error="!!linkedInUrlError" :error-messages="linkedInUrlError" />
                                            <VBtn color="primary" class="flow-validate-btn" rounded="lg"
                                                @click="validateLinkedInUrl">
                                                Validate
                                            </VBtn>
                                        </div>

                                        <div class="flow-search-hint">
                                            <span class="flow-search-hint-dot" />
                                            Paste the search URL directly from LinkedIn
                                        </div>
                                    </template>

                                    <!-- Upload CSV -->
                                    <template v-else-if="selectedImportMethod === 'csv'">
                                        <div class="csv-dropzone-card">
                                            <template v-if="csvSelectedFile">
                                                <!-- Map Properties (shown when file is selected) -->
                                                <div class="csv-map-properties">
                                                    <div class="csv-map-header">
                                                        <h3 class="csv-map-title">
                                                            Map Properties
                                                        </h3>
                                                        <p class="csv-map-hint">
                                                            <VIcon :icon="TickIcon" size="19"
                                                                class="csv-map-hint-icon" />
                                                            Make sure file includes contact name and phone number
                                                        </p>
                                                        <VBtn icon variant="text" color="error" size="small"
                                                            class="csv-map-remove-btn" @click="removeCsvFile">
                                                            <VIcon icon="tabler-trash" size="20" />
                                                        </VBtn>
                                                    </div>
                                                    <div class="csv-map-body">
                                                        <!-- Card: header (Contact Field | CSV Column) + content (columns of fields) -->
                                                        <div class="csv-map-card">
                                                            <div class="csv-map-card-header">
                                                                <span class="csv-map-card-header-label">Contact
                                                                    Field</span>
                                                                <span class="csv-map-card-header-label">CSV
                                                                    Column</span>
                                                            </div>
                                                            <div class="csv-map-card-content">
                                                                <div class="csv-map-col">
                                                                    <div v-for="row in csvMappedRows" :key="row.field"
                                                                        class="csv-map-field-row csv-map-field-row--contact">
                                                                        <VIcon :icon="ProfileIcon" size="24"
                                                                            class="csv-map-field-icon csv-map-field-icon--contact" />
                                                                        <span>{{ row.field }}</span>
                                                                    </div>
                                                                </div>
                                                                <div class="csv-map-col">
                                                                    <div v-for="row in csvMappedRows" :key="row.field"
                                                                        class="csv-map-field-row csv-map-field-row--csv">
                                                                        <VIcon :icon="row.columnIcon" size="24"
                                                                            class="csv-map-field-icon csv-map-field-icon--csv" />
                                                                        <span>{{ row.csvColumn }} ({{ row.count
                                                                        }})</span>
                                                                    </div>
                                                                </div>
                                                            </div>
                                                        </div>
                                                        <div class="csv-unmapped-panel">
                                                            <div class="csv-unmapped-card">
                                                                <div class="csv-unmapped-card-header">
                                                                    <span class="csv-unmapped-card-title">Unmapped
                                                                        Works</span>
                                                                </div>
                                                                <div class="csv-unmapped-card-content">
                                                                    <VTextField variant="outlined" density="compact"
                                                                        placeholder="Search" hide-details
                                                                        class="csv-unmapped-search">
                                                                        <template #prepend-inner>
                                                                            <VIcon icon="tabler-search" size="18" />
                                                                        </template>
                                                                    </VTextField>
                                                                    <div class="csv-unmapped-list">
                                                                        <div v-for="item in csvUnmappedItems"
                                                                            :key="item.name" class="csv-unmapped-row">
                                                                            <VIcon icon="tabler-list" size="24"
                                                                                class="csv-unmapped-row-icon" />
                                                                            <span class="csv-unmapped-row-label">{{
                                                                                item.name }} ({{ item.count }})</span>
                                                                            <span class="csv-unmapped-row-count">({{
                                                                                item.count }})</span>
                                                                        </div>
                                                                    </div>
                                                                    <a href="#" class="csv-unmapped-clear"
                                                                        @click.prevent>Clear All Matched</a>
                                                                </div>
                                                            </div>
                                                        </div>
                                                    </div>
                                                </div>
                                            </template>
                                            <template v-else>
                                                <div class="csv-dropzone" @click="csvFileInputRef?.click()"
                                                    @dragover.prevent @drop.prevent="onCsvDrop">
                                                    <div>
                                                        <div class="csv-dropzone-icon-wrap">
                                                            <VIcon icon="tabler-upload" size="19"
                                                                class="csv-dropzone-icon" />
                                                        </div>
                                                        <div class="csv-dropzone-label">
                                                            Drag a File or click a browse
                                                        </div>
                                                        <div class="csv-dropzone-subtitle">
                                                            File with up to 100 rows works best
                                                        </div>
                                                    </div>
                                                </div>
                                                <a href="#" class="csv-dropzone-download" @click.prevent>
                                                    <VIcon :icon="CloudDownloadIcon" size="19"
                                                        class="csv-dropzone-download-icon" />
                                                    Download a sample file
                                                </a>
                                            </template>
                                            <input ref="csvFileInputRef" type="file" accept=".csv" class="d-none"
                                                @change="onCsvFileSelected">
                                        </div>
                                    </template>

                                    <!-- Inbound Webhook (Lookalike uses modal instead) -->
                                    <template v-else>
                                        <div class="flow-method-header">
                                            Sync leads from Zapier, HubSpot, n8n or other tools in real time.
                                        </div>
                                        <div class="text-caption text-medium-emphasis mt-2">
                                            Webhook configuration UI will appear here.
                                        </div>
                                    </template>
                                </VExpansionPanelText>
                            </VExpansionPanel>
                        </VExpansionPanels>
                    </div>
                </div>

                <!-- Step 2: Sender Profiles -->
                <div v-else class="flow-step-pane flow-step-pane--sender">
                    <div class="sender-tabs-wrap">
                        <VTabs v-model="senderTab" class="sender-tabs" density="comfortable" variant="plain">
                            <VTab value="linkedin" class="sender-tab border-right">LinkedIn Profile</VTab>
                            <VTab value="email" class="sender-tab">Email Accounts</VTab>
                        </VTabs>
                    </div>

                    <!-- LinkedIn Profile section -->
                    <div v-if="senderTab === 'linkedin'" class="sender-section">
                        <div class="sender-section-header">
                            <div class="sender-section-title-wrap">
                                <div>
                                    <div class="sender-section-title-wrap-inner">
                                        <VIcon :icon="LinkedInLogoIcon" size="22" class="sender-section-icon" />
                                        <h3 class="sender-section-title">LinkedIn Profile</h3>
                                    </div>
                                    <p class="sender-section-desc">Pick which LinkedIn profiles you want to use for this
                                        campaign.
                                    </p>
                                </div>
                            </div>
                            <VBtn color="primary" size="small" class="sender-add-btn" @click.prevent>
                                + Add Account
                            </VBtn>
                        </div>


                        <div class="sender-toolbar">
                            <div class="sender-toolbar-left">
                                <span class="sender-show-label">Show</span>
                                <VSelect v-model="senderShowPerPage" :items="[10, 25, 50]" density="compact"
                                    hide-details variant="outlined" class="sender-show-select"
                                    menu-icon="tabler-chevron-down" />
                            </div>
                            <VTextField v-model="senderSearch" placeholder="Search" density="compact" hide-details
                                variant="outlined" class="sender-search" clearable>
                                <template #prepend-inner>
                                    <VIcon icon="tabler-search" size="18" />
                                </template>
                            </VTextField>
                        </div>

                        <div class="sender-table-wrap">
                            <table class="sender-table">
                                <thead>
                                    <tr class="sender-thead-row">
                                        <th class="sender-th sender-th--checkbox">
                                            <VCheckbox :model-value="senderSelectAll" hide-details density="compact"
                                                color="primary" @update:model-value="toggleSenderSelectAll" />
                                        </th>
                                        <th class="sender-th">NAME</th>
                                        <th class="sender-th">HEALTH</th>
                                        <th class="sender-th">DAILY LIMITS</th>
                                        <th class="sender-th">ACCOUNT TYPE</th>
                                        <th class="sender-th">STATUS</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr v-for="profile in senderProfiles" :key="profile.id" class="sender-row">
                                        <td class="sender-td sender-td--checkbox">
                                            <VCheckbox :model-value="selectedSenderIds.includes(profile.id)"
                                                hide-details density="compact" color="primary"
                                                @update:model-value="toggleSenderProfile(profile.id)" />
                                        </td>
                                        <td class="sender-td sender-td--name">
                                            <div class="sender-name-cell">
                                                <VAvatar size="36" color="grey-lighten-2" class="sender-avatar">
                                                    <VImg :src="profile.avatar" :alt="profile.name" />
                                                </VAvatar>
                                                <div class="sender-name-text">
                                                    <span class="sender-name">{{ profile.name }}</span>
                                                    <span class="sender-connections">{{ profile.connections }}
                                                        connections</span>
                                                </div>
                                            </div>
                                        </td>
                                        <td class="sender-td">
                                            <div class="sender-health">
                                                <VProgressCircular :model-value="profile.health" size="44" width="4"
                                                    :color="profile.healthColor" class="sender-health-gauge">
                                                    {{ profile.health }}
                                                </VProgressCircular>
                                            </div>
                                        </td>
                                        <td class="sender-td sender-td--limits">
                                            <span class="sender-limits-chip">{{ profile.dailyLimits }}</span>
                                        </td>
                                        <td class="sender-td">
                                            <span class="sender-account-type">
                                                <img :src="LinkedInPremiumIcon" alt="LinkedIn Premium"
                                                    class="sender-account-icon-img" />
                                                {{ profile.accountType }}
                                            </span>
                                        </td>
                                        <td class="sender-td">
                                            <VChip size="small" :color="profile.statusColor" variant="flat"
                                                class="sender-status-chip">
                                                {{ profile.status }}
                                            </VChip>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>

                    <!-- Email Accounts tab placeholder -->
                    <div v-else class="sender-section">
                        <p class="sender-section-desc">Email accounts configuration will appear here.</p>
                    </div>
                </div>

                <!-- Footer -->
                <div class="flow-footer">
                    <template v-if="activeStep === 'define'">
                        <VBtn color="primary" class="flow-next-btn" :disabled="hasStepError" @click="handleNextStep">
                            Next
                        </VBtn>
                    </template>
                    <template v-else>
                        <VBtn variant="text" color="primary" class="flow-prev-btn" @click="activeStep = 'define'">
                            <VIcon :icon="ArrowTurnBackwardIcon" size="14" class="flow-prev-btn-icon" />
                            Previous
                        </VBtn>
                        <VBtn color="primary" variant="flat" class="flow-submit-btn"
                            :disabled="selectedSenderIds.length === 0" @click="handleSubmit">
                            Submit
                        </VBtn>
                    </template>
                </div>
            </div>
        </VCard>

        <!-- Lookalikes modal (when Lead Lists is selected) -->
        <VDialog v-model="showLookalikeModal" max-width="702" scrim="transparent" persistent
            class="workflow-dialog lookalike-dialog" :class="{ view: showLookalikeModal }">
            <VCard class="workflow-modal-card lookalike-modal-card" elevation="0">
                <div class="lookalike-modal-header">
                    <div class="lookalike-modal-header-text">
                        <h2 class="lookalike-modal-title">Lookalikes</h2>
                        <p class="lookalike-modal-subtitle">Select a lookalike list for this campaign</p>
                    </div>
                    <VBtn icon variant="text" class="lookalike-modal-close" @click="onLookalikeClose">
                        <VIcon icon="tabler-circle-x" size="24" class="lookalike-close-icon" />
                    </VBtn>
                </div>
                <VCardText class="lookalike-modal-body"
                    :class="{ 'lookalike-modal-body--list': lookalikeModalView === 'list' }">
                    <!-- Empty state: no leads, show Create a List button -->
                    <div v-if="lookalikeModalView === 'empty'" class="lookalike-empty-state">
                        <p class="lookalike-empty-title">You don’t have any leads</p>
                        <p class="lookalike-empty-subtitle">Create a lead list to start running campaigns</p>
                        <VBtn color="primary" class="lookalike-create-btn" rounded="lg" @click="onLookalikeCreateList">
                            Create a List
                        </VBtn>
                    </div>
                    <!-- List view: select a lookalike list (shown when user clicks Create a List) -->
                    <div v-else class="lookalike-list-view">
                        <div v-for="list in lookalikeLists" :key="list.id" class="lookalike-list-item"
                            @click="selectedLookalikeListId = list.id">
                            <VIcon icon="tabler-list" size="24" class="lookalike-list-item-icon" />
                            <div class="lookalike-list-item-text">
                                <span class="lookalike-list-item-name">{{ list.name }}</span>
                                <span class="lookalike-list-item-count">({{ list.userCount }})</span>
                            </div>
                            <VCheckbox :model-value="selectedLookalikeListId === list.id" hide-details density="compact"
                                color="primary" class="lookalike-list-item-checkbox"
                                :class="{ 'lookalike-list-item-checkbox--hidden': selectedLookalikeListId !== list.id }"
                                @click.stop
                                @update:model-value="(v) => { selectedLookalikeListId = v ? list.id : (selectedLookalikeListId === list.id ? null : selectedLookalikeListId) }" />
                        </div>
                        <div class="lookalike-list-actions">
                            <a href="#" class="lookalike-add-new-link" @click.prevent>Add New</a>
                        </div>
                    </div>
                </VCardText>
                <VCardActions v-if="lookalikeModalView === 'list'" class="lookalike-modal-footer">
                    <VSpacer />
                    <VBtn variant="flat" class="lookalike-footer-cancel" @click="onLookalikeCancelList">
                        Cancel
                    </VBtn>
                    <VBtn color="primary" variant="flat" class="lookalike-footer-select"
                        :disabled="!selectedLookalikeListId" @click="onLookalikeSelectList">
                        Select List
                    </VBtn>
                </VCardActions>
            </VCard>
        </VDialog>
    </div>
</template>

<script setup lang="ts">
import avatar1 from '@images/avatars/avatar-1.png'
import LinkedInPremiumIcon from '@images/linkdin-prium.png'
import ArrowTurnBackwardIcon from '@images/svg/arrow-turn-backward.svg'
import CalendarIcon from '@images/svg/calendar-04.svg'
import CheckListIcon from '@images/svg/check-list.svg'
import CloudDownloadIcon from '@images/svg/cloud-download.svg'
import DistributeVerticalTopIcon from '@images/svg/distribute-vertical-top.svg'
import GreenCheckIcon from '@images/svg/green-check.svg'
import LinkedInIcon from '@images/svg/linkedin-01.svg'
import LinkedInLogoIcon from '@images/svg/linkedin-logo.svg'
import Note01Icon from '@images/svg/note-01.svg'
import ProfileIcon from '@images/svg/profile.svg'
import Profile2Icon from '@images/svg/profile2.svg'
import TickIcon from '@images/svg/tick-01.svg'
import UserCircleIcon from '@images/svg/user-circle.svg'
import UserListIcon from '@images/svg/user-list.svg'
import UserSearchIcon from '@images/svg/user-search.svg'
import { computed, ref, watch } from 'vue'
const activeStep = ref<'define' | 'sender'>('define')
const importPanel = ref<number[]>([0])
const contentPanel = ref<number[]>([0])
const selectedImportMethod = ref<'linkedin' | 'csv' | 'lookalike' | 'webhook' | null>(null)
const linkedInSearchUrl = ref('')
const linkedInUrlValidated = ref(false)
const linkedInUrlError = ref('')
const csvSelectedFile = ref<File | null>(null)
const csvFileInputRef = ref<HTMLInputElement | null>(null)
const showLookalikeModal = ref(false)
/** Lookalike modal view: 'empty' = no leads message, 'list' = select list (Founder, Tech Profiles, etc.) */
const lookalikeModalView = ref<'empty' | 'list'>('empty')
/** Selected lookalike list id for list view (modal selection state) */
const selectedLookalikeListId = ref<string | null>(null)
/** Confirmed lookalike list id when user clicks "Select List" (enables main Next button) */
const confirmedLookalikeListId = ref<string | null>(null)
/** Mock lookalike lists for list view */
const lookalikeLists = [
    { id: 'founder', name: 'Founder', userCount: '1000+ Users in the List' },
    { id: 'tech-profiles', name: 'Tech Profiles', userCount: '1000+ Users in the List' },
]

/** Mock mapped rows for Map Properties (replace with real CSV parsing later) */
const csvMappedRows = [
    { field: 'Full name', csvColumn: 'Full name', count: 35, columnIcon: UserCircleIcon },
    { field: 'First name', csvColumn: 'First name', count: 3, columnIcon: UserCircleIcon },
    { field: 'Last name', csvColumn: 'Last name', count: 12, columnIcon: UserCircleIcon },
    { field: 'Company Name', csvColumn: 'Company Name', count: 36, columnIcon: Profile2Icon },
    { field: 'Position', csvColumn: 'Position', count: 25, columnIcon: DistributeVerticalTopIcon },
    { field: 'Headline', csvColumn: 'Headline', count: 25, columnIcon: Note01Icon },
]

/** Mock unmapped columns for Unmapped Works panel */
const csvUnmappedItems = [
    { name: 'Location', count: 9 },
    { name: 'Industry', count: 3 },
    { name: 'Notes', count: 9 },
]

/** Valid LinkedIn URL patterns: search/people, sales/search, posts/, groups/, events/ */
const LINKEDIN_URL_PATTERN = /^https?:\/\/(www\.)?linkedin\.com\/(search\/results\/people|sales\/search|posts\/|groups\/|events\/|.*\/posts\/)/i

function validateLinkedInUrl(): boolean {
    const url = linkedInSearchUrl.value.trim()
    linkedInUrlError.value = ''
    if (!url) {
        linkedInUrlValidated.value = false
        linkedInUrlError.value = 'Please enter a LinkedIn URL.'
        return false
    }
    const isValid = LINKEDIN_URL_PATTERN.test(url)
    linkedInUrlValidated.value = isValid
    if (!isValid)
        linkedInUrlError.value = 'Please enter a valid LinkedIn URL (e.g. People search, Sales Navigator, Post or Group URL).'
    return isValid
}

watch(linkedInSearchUrl, () => {
    linkedInUrlValidated.value = false
    linkedInUrlError.value = ''
})

watch(selectedImportMethod, (method) => {
    if (method === 'lookalike')
        showLookalikeModal.value = true
    else
        showLookalikeModal.value = false
})

function onImportMethodCardClick(methodId: 'linkedin' | 'csv' | 'lookalike' | 'webhook') {
    selectedImportMethod.value = methodId
    if (methodId === 'lookalike')
        showLookalikeModal.value = true
}

function onCsvFileSelected() {
    const file = csvFileInputRef.value?.files?.[0]
    csvSelectedFile.value = file ?? null
}

function onCsvDrop(e: DragEvent) {
    const file = e.dataTransfer?.files?.[0]
    if (file)
        csvSelectedFile.value = file
}

function removeCsvFile() {
    csvSelectedFile.value = null
    if (csvFileInputRef.value)
        csvFileInputRef.value.value = ''
}

const isContentFilled = computed(() => {
    if (!selectedImportMethod.value) return false
    if (selectedImportMethod.value === 'linkedin') return linkedInUrlValidated.value
    if (selectedImportMethod.value === 'csv') return !!csvSelectedFile.value
    if (selectedImportMethod.value === 'lookalike') return true
    return false
})

const hasStepError = computed(() => {
    if (activeStep.value !== 'define') return false
    if (selectedImportMethod.value === null) return true
    if (selectedImportMethod.value === 'linkedin')
        return !!linkedInUrlError.value || !linkedInUrlValidated.value
    if (selectedImportMethod.value === 'csv')
        return !csvSelectedFile.value
    return false
})

const contentAccordionTitle = computed(() => {
    const titles: Record<NonNullable<typeof selectedImportMethod.value>, string> = {
        linkedin: 'Paste LinkedIn Search URL',
        csv: 'Upload CSV File',
        lookalike: 'Lookalike Audience',
        webhook: 'Inbound Webhook',
    }
    return selectedImportMethod.value ? titles[selectedImportMethod.value] : ''
})

const cards: Array<{
    id: 'linkedin' | 'csv' | 'lookalike' | 'webhook'
    title: string
    subtitle: string
    icon: typeof LinkedInIcon | string
    downloadLabel: string | null
}> = [
        {
            id: 'linkedin',
            title: 'LinkedIn Search',
            subtitle: 'Basic, Sales Nav, Post, Group or Event URL',
            icon: LinkedInIcon,
            downloadLabel: null,
        },
        {
            id: 'csv',
            title: 'Upload CSV File',
            subtitle: 'Upload LinkedIn profiles via CSV.',
            icon: CalendarIcon,
            downloadLabel: 'Download Sample',
        },
        {
            id: 'lookalike',
            title: 'Lead Lists',
            subtitle: 'Use Lead Finder to find audience.',
            icon: UserListIcon,
            downloadLabel: null,
        },
        {
            id: 'webhook',
            title: 'Inbound Webhook',
            subtitle: 'Sync leads from Zapier, Hubspot, etc.',
            icon: LinkedInIcon,
            downloadLabel: null,
        },
    ]

/** First accordion title: "Choose Import Method" when none selected; else selected method + " Selected" (per Figma) */
const importAccordionTitle = computed(() => {
    if (!selectedImportMethod.value) return 'Choose Import Method'
    const card = cards.find(c => c.id === selectedImportMethod.value)
    const name = card?.title ?? 'Choose Import Method'
    return `${name} Selected`
})

/** Sender Profiles step */
const senderTab = ref<'linkedin' | 'email'>('linkedin')
const senderShowPerPage = ref(10)
const senderSearch = ref('')
const selectedSenderIds = ref<string[]>([])

const senderProfiles = [
    {
        id: 'edgar-jones',
        name: 'Edgar Jones',
        avatar: avatar1,
        connections: '1,250',
        health: 72,
        healthColor: 'warning',
        dailyLimits: 'Invites: 40 / day',
        accountType: 'Premium',
        status: 'Connected',
        statusColor: 'success',
    },
]

const senderSelectAll = computed(() =>
    senderProfiles.length > 0 && selectedSenderIds.value.length === senderProfiles.length)

function toggleSenderSelectAll(v: boolean | null) {
    if (v)
        selectedSenderIds.value = senderProfiles.map(p => p.id)
    else
        selectedSenderIds.value = []
}

function toggleSenderProfile(id: string) {
    const idx = selectedSenderIds.value.indexOf(id)
    if (idx >= 0)
        selectedSenderIds.value = selectedSenderIds.value.filter(i => i !== id)
    else
        selectedSenderIds.value = [...selectedSenderIds.value, id]
}

function handleSubmit() {
    // Placeholder: submit campaign
    activeStep.value = 'define'
}

const handleNextStep = () => {
    if (activeStep.value === 'define')
        activeStep.value = 'sender'
}

function openInNewTab(url: string) {
    window.open(url, '_blank', 'noopener,noreferrer')
}

function onLookalikeCreateList() {
    selectedLookalikeListId.value = confirmedLookalikeListId.value
    lookalikeModalView.value = 'list'
}

function onLookalikeCancelList() {
    lookalikeModalView.value = 'empty'
    selectedLookalikeListId.value = null
}

function onLookalikeSelectList() {
    if (selectedLookalikeListId.value)
        confirmedLookalikeListId.value = selectedLookalikeListId.value
    showLookalikeModal.value = false
    lookalikeModalView.value = 'empty'
    selectedLookalikeListId.value = null
}

function onLookalikeClose() {
    showLookalikeModal.value = false
    lookalikeModalView.value = 'empty'
    selectedLookalikeListId.value = null
}

watch(showLookalikeModal, (open) => {
    if (!open) {
        lookalikeModalView.value = 'empty'
        selectedLookalikeListId.value = null
    }
})
</script>

<route lang="yaml">
meta:
  navActiveLink: Campaign
</route>

<style scoped>
.flow-page {
    padding: 21px 0;
    height: 100%;
    min-height: 88vh;
}

.flow-card {
    border-radius: 8px;
    padding: 23px 25px;
    display: flex;
    height: 100%;
    min-height: 88vh;
    flex-direction: column;
}

.flow-breadcrumb {
    font-size: 13px;
}

.flow-breadcrumb .link {
    color: rgb(var(--v-theme-workflow-title));
}

.flow-frame {
    border-radius: 12px;
    background-color: rgb(var(--v-theme-surface));
    flex: 1;
    display: flex;
    flex-direction: column;
    min-height: 0;
}

.flow-steps {
    display: flex;
    align-items: center;
    gap: 6px;
    border-radius: 10px;
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
    background-color: rgb(var(--v-theme-surface));
    padding: 4px;
    margin-bottom: 16px;
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

.flow-step--active {
    background-color: rgb(var(--v-theme-primary)) !important;
    color: rgb(var(--v-theme-on-primary)) !important;
}

/* First step when on next page: completed/visited state (light blue bg, dark icon, blue underline) */
.flow-step--completed.icon-wrapper {
    background-color: rgba(var(--v-theme-primary), 0.12) !important;
    color: rgb(var(--v-theme-primary)) !important;
}

.flow-step--completed.icon-wrapper .flow-step-icon {
    color: rgb(var(--v-theme-primary)) !important;
}

.flow-step--inactive {
    background-color: rgb(var(--v-theme-surface));
    color: rgba(var(--v-theme-workflow-title), 0.8);
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
    z-index: 2;
    width: 3px;
    background-color: rgb(var(--v-theme-workflow-header-bg)) !important;
}

.progress-bar-inner:after {
    content: '';
    position: absolute;
    left: 0;
    top: -50px;
    bottom: 0;
    z-index: 1;
    width: 3px;
    background-color: rgb(var(--v-theme-workflow-header-bg)) !important;
}


.progress-bar-inner-fill {
    width: 14px;
    height: 14px;
    border-radius: 50%;
    border: 2px solid rgb(var(--v-theme-primary));
    background-color: rgb(var(--v-theme-surface));
    position: absolute;
    left: -6px;
    z-index: 3;
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

.flow-content-accordion {
    margin: 0 !important;
}

.progress-bar--has-selection .progress-bar-check-icon,
.progress-bar--content-filled .progress-bar-inner-fill .progress-bar-check-icon {
    display: inline-flex;
}



.flow-step-pane--sender {
    padding: 7px 8px 8px;
    margin-top: 7px;
}

/* Sender Profiles tabs – segmented control: active = blue bg + white text, inactive = white + blue text + border */
.sender-tabs-wrap {
    margin-bottom: 24px;
    display: inline-flex;

}

.sender-tabs {
    border: 1px solid rgb(var(--v-theme-primary)) !important;
    border-radius: 5px;
}

.border-right {
    border-right: 1px solid rgb(var(--v-theme-primary)) !important;
}

.sender-tabs :deep(.v-tabs-container),
.sender-tabs :deep(.v-tabs) {
    border-radius: inherit;
}

.sender-tabs :deep(.v-tab) {
    text-transform: none;
    font-weight: 600;
    font-size: 14px;
    border-radius: 0;
    background-color: transparent;
    color: rgb(var(--v-theme-primary));
}

.sender-tabs :deep(.v-tab:first-child) {
    border-radius: 5px 0 0 5px;
}

.sender-tabs :deep(.v-tab:last-child) {
    border-radius: 0 5px 5px 0;
}

.sender-tabs :deep(.v-tab--selected),
.sender-tabs :deep(.v-tab[aria-selected="true"]) {
    background-color: rgba(var(--v-theme-primary), 0.15) !important;
    color: rgb(var(--v-theme-primary)) !important;
}

.sender-tabs :deep(.v-tab__slider),
.sender-tabs :deep(.v-btn::after) {
    display: none !important;
}


.sender-tabs :deep(.v-tabs-slider),
.sender-tabs :deep(.v-tab-indicator) {
    display: none !important;
}

.sender-tab {
    padding: 10px 16px;
    font-size: 14px;
    font-weight: 500 !important;
    line-height: 100%;
}

.sender-section {
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
    border-radius: 12px;

}

.sender-section-title-wrap-inner {
    display: flex;
    align-items: center;
    gap: 5px;
}

.sender-section-desc {
    color: rgb(var(--v-theme-workflow-text-secondary));
    font-size: 12px;
    line-height: 100%;
    margin-top: 8px;
    margin-bottom: 0;
}

.sender-section-header {
    display: flex;
    align-items: center;
    padding: 17px 20px 17px 30px;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 12px;
    margin-bottom: 0;
    border-bottom: 1px solid rgba(var(--v-theme-grey-200), 1);
}

.sender-section-title-wrap {
    display: flex;
    align-items: center;
    gap: 10px;
}

.sender-section-icon {
    color: rgb(var(--v-theme-primary));
}

.sender-section-title {
    margin: 0;
    font-size: 18px;
    font-weight: 600;
    color: rgb(var(--v-theme-on-surface));
}

.sender-add-btn {
    text-transform: none;
    font-weight: 600;
}



.sender-toolbar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 12px;
    padding: 25px 20px 25px 30px;
}

.sender-toolbar-left {
    display: flex;
    align-items: center;
    gap: 8px;
}

.sender-show-label {
    font-size: 14px;
    line-height: 21px;
    font-weight: 400;
    color: rgb(var(--v-theme-workflow-text-secondary));
}

.sender-show-select {
    max-width: 80px;
}

.sender-show-select :deep(.v-field) {
    border: none !important;
    box-shadow: none !important;
    background-color: rgb(var(--v-theme-surface));
    border-radius: 6px;
}

.sender-show-select :deep(.v-field__outline) {
    display: none;
}

.sender-show-select :deep(.v-field__append-inner .v-icon),
.sender-show-select :deep(.v-field [class*="append-inner"] .v-icon) {
    color: rgb(var(--v-theme-on-surface)) !important;
    font-size: 18px;
    opacity: 0.85;
}

.sender-search {
    max-width: 174px;
    width: 100%;
}

.sender-search :deep(.tabler-search) {
    width: 12px;
    height: 12px;
}

.sender-table-wrap {
    border: 1px solid rgba(var(--v-theme-grey-200), 0.8);
    border-radius: 10px;
    overflow: hidden;
    background-color: rgb(var(--v-theme-surface));
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.04);
}

.sender-table {
    width: 100%;
    border-collapse: collapse;
}

.sender-thead-row .sender-th {
    background-color: rgb(var(--v-theme-workflow-header-bg));
    border-bottom: 1px solid rgba(var(--v-theme-grey-200), 0.9);
    padding: 10px 15px;
    font-size: 12px;
    font-weight: 600;
    letter-spacing: 1px;
    font-size: 12px;
    line-height: 100%;
    text-transform: uppercase;
    color: rgb(var(--v-theme-workflow-text-main));
}

.sender-th {
    text-align: left;
}

.sender-th--checkbox {
    width: 52px;
    padding-left: 16px;
    vertical-align: middle;
}

.sender-row {
    background-color: rgb(var(--v-theme-surface));
    border-bottom: 1px solid rgba(var(--v-theme-grey-200), 0.6);
}

.sender-row:last-child {
    border-bottom: none;
}

.sender-td {
    padding: 14px 16px;
    font-size: 14px;
    color: rgb(var(--v-theme-on-surface));
    vertical-align: middle;
    background-color: rgb(var(--v-theme-surface));
}

.sender-td--checkbox {
    width: 52px;
}

.sender-td--name {
    min-width: 220px;
}

.sender-name-cell {
    display: flex;
    align-items: center;
    gap: 12px;
}

.sender-avatar {
    flex-shrink: 0;
}

.sender-name-text {
    display: flex;
    flex-direction: column;
    gap: 2px;
}

.sender-name {
    font-weight: 600;
    font-size: 14px;
    color: rgb(var(--v-theme-on-surface));
}

.sender-connections {
    font-size: 13px;
    color: rgba(var(--v-theme-on-surface), 0.65);
}

.sender-health {
    display: flex;
    align-items: center;
}

.sender-health-gauge {
    font-size: 13px;
    font-weight: 700;
}

.sender-health-gauge :deep(.v-progress-circular__content) {
    color: rgb(var(--v-theme-warning));
}

.sender-td--limits {
    font-size: 13px;
}

.sender-limits-chip {
    display: inline-block;
    padding: 6px 12px;
    border-radius: 6px;
    border: 1px solid rgba(var(--v-theme-grey-300), 0.8);
    background-color: rgb(var(--v-theme-surface));
    color: rgb(var(--v-theme-on-surface));
    font-size: 13px;
}

.sender-account-type {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    font-size: 13px;
    color: rgb(var(--v-theme-on-surface));
}

.sender-account-icon {
    opacity: 0.85;
    color: rgba(var(--v-theme-on-surface), 0.8);
}

.sender-account-icon-img {
    width: 16px;
    height: 16px;
    object-fit: contain;
    flex-shrink: 0;
    vertical-align: middle;
}

.sender-status-chip {
    font-weight: 500;
    text-transform: none;
    border-radius: 6px;
}

.sender-status-chip :deep(.v-chip__content) {
    background: transparent !important;
    color: inherit !important;
}

.flow-footer {
    display: flex;
    justify-content: flex-end;
    align-items: center;
    gap: 12px;
    margin-top: auto;
    padding-top: 18px;
}

.flow-prev-btn {
    background: transparent !important;
    color: rgb(var(--v-theme-primary)) !important;
    text-transform: none;
    font-weight: 500;
    font-size: 14px;
    padding: 10px 16px;
    border-radius: 8px;
    box-shadow: none !important;
    min-width: 0;
}

.flow-prev-btn .flow-prev-btn-icon {
    color: rgb(var(--v-theme-primary)) !important;
    margin-right: 6px;
}

.flow-submit-btn {
    display: flex;
    padding: 10px 24px;
    font-weight: 500;
    font-size: 14px;
    text-transform: none;
    border-radius: 8px;
    background-color: rgb(var(--v-theme-primary)) !important;
    color: rgb(var(--v-theme-on-primary)) !important;
    box-shadow: 0 2px 4px rgba(var(--v-theme-primary), 0.35);
}

.flow-submit-btn:hover:not(.v-btn--disabled) {
    box-shadow: 0 2px 8px rgba(var(--v-theme-primary), 0.4);
    opacity: 0.95;
}

.flow-import {
    margin-top: 6px;
}

.flow-import-panels {
    background: transparent !important;
    box-shadow: none !important;
}

.flow-content-panels :deep(.v-expansion-panel__shadow),
.flow-import-panels :deep(.v-expansion-panel__shadow) {
    display: none !important;
}

.flow-content-accordion :deep(.v-expansion-panel),
.flow-import-accordion :deep(.v-expansion-panel__shadow) {
    border: none !important;
}

.flow-content-accordion :deep(.flow-content-header),
.flow-import-header {
    display: flex;
    align-items: center;
    gap: 10px;
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
    border-radius: 5px !important;
    padding: 15px 9px !important;
    font-size: 15px;
    font-weight: 500;
    line-height: 140%;
}

.flow-import-header--has-selection {
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
}

.flow-content-header--filled {
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
}

.flow-import-panels :deep(.flow-import-body),
.flow-import-panels :deep(.v-expansion-panel) {
    border: none !important;
}


.flow-import-panels :deep(.v-expansion-panel) {
    border-radius: 10px;
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
}

.flow-import-header {
    padding: 12px 14px;
    min-height: 48px;
}

.flow-import-body {
    border-top: 1px solid rgba(var(--v-theme-grey-200), 1);
    padding: 0;
    margin-top: 18px;
}

.flow-import-body :deep(.v-expansion-panel-text__wrapper) {
    padding: 0;
    padding-left: 11px;
    padding-right: 11px;

}



.flow-radio {
    width: 16px;
    height: 16px;
    border-radius: 999px;
    border: 2px solid rgba(var(--v-theme-grey-400), 1);
    box-sizing: border-box;
    background-color: rgb(var(--v-theme-surface));
}



.flow-import-title {
    font-size: 14px;
    font-weight: 500;
    color: rgb(var(--v-theme-workflow-title));
}

.flow-import-toggle-icon {
    color: rgba(var(--v-theme-workflow-title), 0.6);
}

.flow-radio--outline {
    border: 2px solid rgba(var(--v-theme-primary), 1);
    background-color: rgb(var(--v-theme-surface));
}

.flow-content-accordion {
    margin-top: 18px;
}

.flow-content-panels {
    background: transparent !important;
}

.flow-content-panels :deep(.v-expansion-panel) {
    border-radius: 10px;
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
}

.flow-content-header {
    padding: 12px 14px;
    min-height: 48px;
    display: flex;
    align-items: center;
    gap: 10px;
}

.flow-content-step-badge {
    margin-left: 7px;
    font-size: 12px;
    font-weight: 500;
    padding: 2px 13px;
    border-radius: 5px;
    background-color: rgb(var(--v-theme-workflow-header-bg)) !important;
    color: rgb(var(--v-theme-workflow-footer-close-text));
}

.flow-content-step-badge :deep(.v-chip__underlay) {
    background: rgb(var(--v-theme-workflow-header-bg)) !important;
}


.flow-content-body {
    padding: 0 !important;
    padding-top: 0 !important;
}

.linkedin-search-body :deep(.v-expansion-panel-text__wrapper) {
    padding: 27px 26px;
    margin-top: 19px;
    border-radius: 5px;
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
}

.csv-search-body :deep(.v-expansion-panel-text__wrapper) {
    margin-top: 19px;
    border-radius: 5px;
    padding: 0;
}

.csv-search-body :deep(.csv-map-properties) {
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
    border-radius: 5px;
}

.csv-search-body :deep(.csv-map-title) {
    font-size: 23px;
    font-weight: 500;
    line-height: 140%;
}

.csv-search-body :deep(.csv-map-hint) {
    font-size: 16px;
    font-weight: 400;
    line-height: 140%;
    display: flex;
    align-items: center;
    gap: 1px;
    margin-top: 11px;
    margin-bottom: 0;
    color: rgba(var(--v-theme-workflow-title), 1);
}

.csv-search-body :deep(.csv-map-header) {
    padding: 23px 16px 27px 16px;
    border-bottom: 1px solid rgba(var(--v-theme-grey-200), 1);
}

.csv-search-body :deep(.csv-map-body) {
    margin: 21px 21px 23px 11px;
    align-items: stretch;
    border-radius: 5px;
    width: 100%;

}

.csv-search-body :deep(.csv-map-card) {
    border: 1px solid rgba(var(--v-theme-grey-200), 1);
    border-radius: 10px;
    overflow: hidden;
    flex: unset;
    max-width: 594px;
    width: 100%;
}

.csv-dropzone-card {
    border-radius: 12px;
    background-color: rgb(var(--v-theme-surface));
}

.csv-dropzone {
    border-radius: 10px;
    border: 2px dashed rgba(var(--v-theme-primary), 0.45);
    padding: 48px 29px 29px 29px;
    text-align: center;
    background-color: rgba(var(--v-theme-primary), 0.04);
    cursor: pointer;
    max-height: 160px;
    height: 100%;
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
    margin-bottom: 11px;
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
    line-height: 25px;
}

.csv-dropzone-browse {
    color: rgb(var(--v-theme-primary));
    cursor: pointer;
    text-decoration: none;
}

.csv-dropzone-subtitle {
    font-size: 12px;
    font-weight: 400;
    color: rgba(var(--v-theme-workflow-title), 1);
    margin-top: 1px;
    line-height: 140%;
}

.csv-dropzone-download {
    display: inline-flex;
    align-items: center;
    gap: 3px;
    margin-top: 12px;
    font-size: 12px;
    color: rgba(var(--v-theme-workflow-title), 1);
    text-decoration: none;
    line-height: 140%;
    font-weight: 400;
}

.csv-dropzone-download-icon {
    width: 19px;
    height: 19px;
}

.csv-dropzone-download:hover {
    text-decoration: underline;
}

.csv-file-selected {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 12px;
    padding: 14px 16px;
    border-radius: 10px;
    border: 1px solid rgba(var(--v-theme-primary), 0.3);
    background-color: rgba(var(--v-theme-primary), 0.06);
}

.csv-file-name {
    font-size: 14px;
    font-weight: 500;
    color: rgb(var(--v-theme-workflow-title));
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}

.csv-file-remove {
    flex-shrink: 0;
    text-transform: none;
    font-weight: 500;
}

/* Map Properties (when CSV file is selected) */
.csv-map-properties {
    width: 100%;
}

.csv-map-header {
    position: relative;
    margin-bottom: 16px;
    padding-right: 40px;
}

.csv-map-title {
    font-size: 16px;
    font-weight: 600;
    color: rgb(var(--v-theme-workflow-title));
    margin: 0 0 6px 0;
}


.csv-map-hint-icon {
    flex-shrink: 0;
}

.csv-map-remove-btn {
    position: absolute;
    top: 4px;
    right: 1px;
}

.csv-map-body {
    display: flex;
    align-items: stretch;
    gap: 24px;
    flex-wrap: wrap;
}

/* Card: header (Contact Field | CSV Column) + content (two columns) */
.csv-map-card {
    flex: 1;
    min-width: 280px;
    background-color: rgb(var(--v-theme-surface));
    border: 1px solid rgba(var(--v-theme-grey-200), 0.8);
    border-radius: 10px;
    overflow: hidden;
}

.csv-map-card-header {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 18px 17px;
    border-bottom: 1px solid rgba(var(--v-theme-grey-200), 0.8);
    background-color: rgb(var(--v-theme-surface));
}

.csv-map-card-header-label {
    flex: 1;
    font-size: 14px;
    font-weight: 600;
    line-height: 100%;
    color: rgb(var(--v-theme-workflow-step-label));
}

.csv-map-card-content {
    display: flex;
    gap: 19px;
    padding: 23px 12px 19px 12px;
}

.csv-map-col {
    flex: 1;
    min-width: 0;
    display: flex;
    flex-direction: column;
    gap: 8px;
}

.csv-map-field-row {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 7px 16px;
    border-radius: 5px;
    border: 1px solid rgb(var(--v-theme-workflow-border-subtle));
    background-color: rgb(var(--v-theme-surface));
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.04);
    font-size: 14px;
    line-height: 100%;
    font-weight: 400;
    color: rgb(var(--v-theme-workflow-step-label));
}

.csv-map-field-row--csv {
    background-color: rgba(var(--v-theme-on-surface), 0.03);
}

.csv-map-field-icon {
    flex-shrink: 0;
}

.csv-map-field-icon--contact {
    color: rgb(var(--v-theme-success));
}

.csv-map-field-icon--csv {
    color: rgb(var(--v-theme-on-surface));
}

.csv-map-row {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 10px 12px;
    border-radius: 8px;
    border: 1px solid rgba(var(--v-theme-grey-200), 0.8);
    background-color: rgb(var(--v-theme-surface));
    margin-bottom: 8px;
    font-size: 14px;
    color: rgb(var(--v-theme-workflow-title));
}

.csv-map-row:last-child {
    margin-bottom: 0;
}

.csv-map-row-icon {
    flex-shrink: 0;
    color: rgba(var(--v-theme-workflow-title), 0.6);
}

.csv-unmapped-panel {
    width: 100%;
    max-width: 323px;
    flex-shrink: 0;
    align-self: stretch;
    display: flex;
}

/* Unmapped Works card: card > card header > card content (stretch to match left panel height) */
.csv-unmapped-card {
    flex: 1;
    display: flex;
    flex-direction: column;
    min-height: 0;
    background-color: rgb(var(--v-theme-surface));
    border: 1px solid rgba(var(--v-theme-grey-200), 0.8);
    border-radius: 10px;
    overflow: hidden;
}

.csv-unmapped-card-header {
    flex-shrink: 0;
    padding: 18px 17px;
    display: flex;
    border-bottom: 1px solid rgba(var(--v-theme-grey-200), 0.6);
}

.csv-unmapped-card-title {
    font-size: 14px;
    font-weight: 600;
    line-height: 100%;
    color: rgb(var(--v-theme-on-surface));
}

/* Content keeps natural size; only outer card stretches */
.csv-unmapped-card-content {
    flex-shrink: 0;
    padding: 14px 16px 18px;
}

.csv-unmapped-search {
    margin-bottom: 10px;
    margin-top: 3px;
}

.csv-unmapped-search :deep(.v-field) {
    font-size: 13px;
}

.csv-unmapped-list {
    margin-bottom: 16px;
}

.csv-unmapped-row {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 10px 12px;
    border-radius: 8px;
    border: 1px solid rgba(var(--v-theme-grey-200), 0.8);
    background-color: rgb(var(--v-theme-workflow-header-bg));
    margin-bottom: 8px;
    font-size: 14px;
    color: rgb(var(--v-theme-on-surface));
}

.csv-unmapped-row:last-child {
    margin-bottom: 0;
}

.csv-unmapped-row-icon {
    flex-shrink: 0;
    color: rgba(var(--v-theme-on-surface), 0.7);
}

.csv-unmapped-row-label {
    flex: 1;
    min-width: 0;
}

.csv-unmapped-row-count {
    flex-shrink: 0;
    color: rgba(var(--v-theme-on-surface), 0.8);
}

.csv-unmapped-clear {
    display: block;
    text-align: right;
    font-size: 13px;
    color: rgb(var(--v-theme-primary));
    text-decoration: none;
}

.csv-unmapped-clear:hover {
    text-decoration: underline;
}

.flow-import-row {
    display: flex;
    flex-wrap: wrap;
    align-items: stretch;
    gap: 19px;
    margin: 0;
}

.flow-import-col {
    min-width: 0;
    max-width: 174px;
}

.flow-import-card {
    border-radius: 8px;
    border: 1px solid transparent;
    padding: 19px 12px;
    background-color: rgb(var(--v-theme-workflow-header-bg));
    cursor: pointer;
    max-width: 174px;
    width: 100%;
    height: 100%;
    position: relative;
    border: 1px solid rgb(var(--v-theme-workflow-header-bg));
    transition: border-color 0.15s ease, box-shadow 0.15s ease, background-color 0.15s ease;
}

.flow-import-card-icon {
    width: 30px;
    height: 30px;
    border-radius: 8px;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    color: rgb(var(--v-theme-workflow-title), 0.6);
    margin-bottom: 10px;
}

.flow-import-card-title {
    font-size: 14px;
    font-weight: 600;
    color: rgb(var(--v-theme-workflow-title), 0.6);
    margin-bottom: 4px;
}

.flow-import-card-subtitle {
    font-size: 12px;
    line-height: 18px;
    color: rgba(var(--v-theme-workflow-title), 0.6);
}

.flow-import-card--selected {
    border-color: rgb(var(--v-theme-primary));
    box-shadow: 0 0 0 1px rgba(var(--v-theme-primary), 0.9);
    background-color: rgba(var(--v-theme-primary), 0.03);
}

.flow-import-card--selected .flow-import-card-icon {
    color: rgb(var(--v-theme-primary));
}

.flow-import-card--selected .flow-import-card-title,
.flow-import-card--selected .flow-import-card-subtitle {
    color: rgba(var(--v-theme-workflow-title), 1);
}

.flow-import-card--selected .flow-import-card-link {
    color: rgb(var(--v-theme-primary), 1);
}

.flow-import-card-badge {
    position: absolute;
    inset-block-start: 8px;
    inset-inline-end: 8px;
    inline-size: 20px;
    block-size: 20px;
    border-radius: 6px;
    background-color: rgb(var(--v-theme-primary));
    color: rgb(var(--v-theme-on-primary));
    display: flex;
    align-items: center;
    justify-content: center;
}

.flow-import-card-link {
    color: rgb(var(--v-theme-primary), 0.6);
    text-decoration: none;
    margin-left: 4px;
    font-weight: 500;
}

.flow-import-card-link:hover {
    text-decoration: underline;
}

.flow-method-detail {
    margin-top: 18px;
}

.flow-method-header-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 11px;
}

.flow-method-header {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 13px;
    line-height: 1.5;
    color: rgba(var(--v-theme-workflow-title), 0.9);
}

.flow-method-header-icon {
    flex-shrink: 0;
    border-radius: 50%;
    color: rgb(var(--v-theme-primary));
    display: flex;
    align-items: center;
    justify-content: center;
}

.flow-method-header-text {
    flex: 1;
    min-width: 0;
    color: rgba(var(--v-theme-workflow-title), 1);
}

.flow-search-guide-link {
    display: inline-flex;
    align-items: center;
    font-size: 13px;
    color: rgb(var(--v-theme-primary));
    text-decoration: none;
}

.flow-search-guide-link:hover {
    text-decoration: underline;
}

.flow-link {
    color: rgb(var(--v-theme-primary));
    text-decoration: none;
}

.flow-link:hover {
    text-decoration: underline;
}

.flow-search-row {
    display: flex;
    align-items: stretch;
    gap: 12px;
    flex-wrap: wrap;
}

.flow-text-field {
    flex: 1;
    min-width: 200px;
}

.flow-input-linkedin-icon {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 32px;
    height: 32px;
    border-radius: 6px;
    background-color: rgba(var(--v-theme-on-surface), 0.06);
    color: rgb(var(--v-theme-primary));
}

.flow-validate-btn {
    flex-shrink: 0;
    min-height: 40px;
}

.flow-search-error {
    display: flex;
    align-items: center;
    margin-top: 8px;
    font-size: 12px;
    line-height: 1.4;
    color: rgb(var(--v-theme-error));
}

.flow-search-row :deep(.v-input__details) {
    padding-inline-start: 0 !important;
}

.flow-search-hint {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 12px;
    line-height: 1.4;
    color: rgba(var(--v-theme-workflow-title), 0.6);
    margin-top: 8px;
}

.flow-search-hint-dot {
    width: 4px;
    height: 4px;
    border-radius: 50%;
    margin-left: 2px;
    background-color: rgb(var(--v-theme-primary));
    box-shadow: 0 0 0 2px rgba(var(--v-theme-primary), 0.25), 0 0 8px 2px rgba(var(--v-theme-primary), 0.35);
    flex-shrink: 0;
}



.flow-next-btn {
    padding: 10px 22px;
    font-weight: 500;
    background-image: linear-gradient(90deg, rgb(var(--v-theme-primary)) 0%, rgb(var(--v-theme-primary-gradient-end)) 100%);
    color: rgb(var(--v-theme-on-primary)) !important;
    border-radius: 5px;
    line-height: 100%;
}

@media (max-width: 959px) {
    .flow-card {
        padding: 16px 12px 18px;
    }

    .flow-frame {
        padding: 14px 10px 16px;
    }
}
</style>

<!-- Lookalikes modal: unscoped so teleported dialog content is styled. Uses theme variables. -->
<style lang="scss">
/* Dark dimmed backdrop: target overlay that contains this dialog */
.v-overlay .v-overlay__scrim {
    background: rgba(var(--v-theme-workflow-scrim), 0.5);
    backdrop-filter: blur(2px);
}

.lookalike-modal-card.workflow-modal-card {
    border-radius: 10px;
    padding: 0;
    box-shadow: 0 12px 40px rgba(var(--v-shadow-key-umbra-color), 0.15);
    border: 1px solid rgba(var(--v-theme-grey-200), 0.8);
    background-color: rgb(var(--v-theme-surface));
    overflow: hidden;
    max-width: 702px;
    width: 100%;
}

.lookalike-modal-card .lookalike-modal-header {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 16px;
    padding: 19px 25px;
    border-bottom: 1px solid rgba(var(--v-theme-grey-200), 0.5);
    background-color: rgb(var(--v-theme-workflow-header-bg));
    position: relative;
}

.lookalike-modal-card .lookalike-modal-header-text {
    flex: 1;
    min-width: 0;
}

.lookalike-modal-card .lookalike-modal-title {
    margin-bottom: 3px;
    font-size: 24px;
    font-weight: 500 !important;
    line-height: 100%;
    color: rgb(var(--v-theme-workflow-title));
}

.lookalike-modal-card .lookalike-modal-subtitle {
    font-size: 14px;
    font-weight: 400 !important;
    line-height: 100%;
    margin: 0;
    color: rgb(var(--v-theme-workflow-step-label));
}

.lookalike-empty-state {
    height: 477px !important;
}

.lookalike-modal-card .lookalike-modal-close {
    flex-shrink: 0;
    margin-top: -2px;
    margin-right: -4px;
}

.lookalike-modal-card .lookalike-close-icon {
    color: rgb(var(--v-theme-grey-600));
}

.lookalike-modal-card .lookalike-modal-body {
    padding: 56px 32px 64px;
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 240px;
    background-color: rgb(var(--v-theme-surface));
}

.lookalike-modal-card .lookalike-modal-body--list {
    align-items: flex-start;
    padding-top: 24px;
    padding-bottom: 24px;
    min-height: 0;
}

.lookalike-modal-card .lookalike-empty-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    margin-top: -50px;
}

.lookalike-modal-card .lookalike-empty-title {
    margin-bottom: 8px;
    font-size: 28px;
    font-weight: 600 !important;
    /* 600 in browser matches Figma 500 visual weight */
    line-height: 36px;
    color: rgb(var(--v-theme-workflow-title));
    font-family: var(--v-font-family-roboto);
}

.lookalike-modal-card .lookalike-empty-subtitle {
    font-size: 14px;
    font-weight: 400;
    line-height: 20px;
    font-family: var(--v-font-family-roboto);
    letter-spacing: 0.25px;
    color: rgb(var(--v-theme-workflow-title));
    margin-bottom: 24px;
}

.lookalike-modal-card .lookalike-create-btn {
    padding: 10px 22px;
    font-size: 14px;
    font-weight: 500 !important;
    text-transform: none;
    letter-spacing: 0.4px;
    border-radius: 8px;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    background-image: linear-gradient(90deg, rgb(var(--v-theme-primary)) 0%, rgb(var(--v-theme-primary-gradient-end)) 100%);
    color: rgb(var(--v-theme-on-primary)) !important;
}

.lookalike-modal-card .lookalike-create-btn:hover {
    background: linear-gradient(180deg, rgb(var(--v-theme-primary)) 0%, rgb(var(--v-theme-primary-gradient-end)) 100%);
    box-shadow: 0 2px 8px rgba(var(--v-theme-primary), 0.35);
}

/* Lookalike list view (select list) */
.lookalike-modal-card .lookalike-list-view {
    width: 100%;
    padding: 0;
}

.lookalike-modal-card .lookalike-list-item {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 14px 16px;
    border: 1px solid rgb(var(--v-theme-workflow-header-bg));
    border-radius: 10px;
    margin-bottom: 12px;
    cursor: pointer;
    background-color: rgb(var(--v-theme-workflow-header-bg));
    transition: border-color 0.2s, background-color 0.2s;
}

.lookalike-modal-card .lookalike-list-item:hover {
    border-color: rgba(var(--v-theme-primary), 0.4);
    background-color: rgba(var(--v-theme-primary), 0.04);
}

.lookalike-modal-card .lookalike-list-item-icon {
    color: rgba(var(--v-theme-on-surface), 0.6);
    flex-shrink: 0;
}

.lookalike-modal-card .lookalike-list-item-text {
    flex: 1;
    min-width: 0;
    display: flex;
    flex-wrap: wrap;
    align-items: baseline;
    gap: 6px;
}

.lookalike-modal-card .lookalike-list-item-name {
    font-size: 15px;
    font-weight: 600;
    color: rgb(var(--v-theme-workflow-text-main));
}

.lookalike-modal-card .lookalike-list-item-count {
    font-size: 14px;
    font-weight: 400;
    color: rgb(var(--v-theme-workflow-text-secondary));
}

.lookalike-modal-card .lookalike-list-item-checkbox {
    flex-shrink: 0;
}

.lookalike-modal-card .lookalike-list-item-checkbox--hidden {
    opacity: 0;
}

.lookalike-modal-card .lookalike-list-actions {
    display: flex;
    justify-content: flex-end;
    margin-top: 12px;
    margin-bottom: 20px;
}

.lookalike-modal-card .lookalike-add-new-link {
    font-size: 14px;
    font-weight: 500;
    color: rgb(var(--v-theme-primary));
    text-decoration: none;
}

.lookalike-modal-card .lookalike-add-new-link:hover {
    text-decoration: underline;
}

.lookalike-modal-card .lookalike-modal-footer {
    display: flex;
    justify-content: flex-end;
    align-items: center;
    gap: 12px;
    padding: 16px 24px 20px;
    border-top: 1px solid rgba(var(--v-theme-grey-200), 0.6);
    background-color: rgb(var(--v-theme-surface));
}

.lookalike-modal-card .lookalike-footer-cancel {
    font-weight: 500;
    text-transform: none;
    background-color: rgb(var(--v-theme-grey-300)) !important;
    color: rgb(var(--v-theme-grey-700)) !important;
    border-radius: 6px;
    padding: 10px 20px;
    display: inline-flex;
}

.lookalike-modal-card .lookalike-footer-cancel:hover {
    background-color: rgb(var(--v-theme-grey-400)) !important;
}

.lookalike-modal-card .lookalike-footer-select {
    font-weight: 500;
    display: inline-flex;
    text-transform: none;
    background-image: linear-gradient(90deg, rgb(var(--v-theme-primary)) 0%, rgb(var(--v-theme-primary-gradient-end)) 100%);
    color: rgb(var(--v-theme-on-primary)) !important;
    border-radius: 6px;
    padding: 10px 20px;
}

.lookalike-modal-card .lookalike-footer-select:hover {
    opacity: 0.92;
}
</style>
