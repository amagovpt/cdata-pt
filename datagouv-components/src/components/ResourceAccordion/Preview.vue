<template>
  <div>
    <SimpleBanner
      v-if="hasError"
      type="warning"
      class="flex items-center space-x-2"
    >
      <RiErrorWarningLine class="shink-0 size-6" />
      <span>{{ t("L'aperçu de ce fichier n'a pas pu être chargé.") }}</span>
    </SimpleBanner>
    <PreviewLoader v-else-if="loading" />
    <div
      v-else
      class="-mx-4"
    >
      <div class="bg-blue-100 text-datagouv fr-hidden fr-unhidden-md p-4">
        <div class="fr-grid-row fr-grid-row--middle fr-grid-row--gutters">
          <div
            class="fr-col-auto"
            v-html="franceSvg"
          />
          <div class="fr-col">
            <p class="fr-text--bold fr-m-0">
              {{ t("Explorer les données en détail") }}
            </p>
            <p class="fr-text--sm fr-m-0 f-italic">
              {{ t("Utiliser notre outil pour obtenir un aperçu des données, en savoir plus sur les différentes colonnes ou réaliser des filtres et des tris.") }}
            </p>
          </div>
          <p class="fr-col-auto fr-my-0">
            <BrandedButton
              :href="resource.preview_url"
              :icon="RiExternalLinkFill"
              icon-right
            >
              {{ t("Explorer les données") }}
            </BrandedButton>
          </p>
        </div>
      </div>
      <div class="fr-table fr-table--no-background fr-p-0 fr-m-0">
        <table class="fr-mb-3w">
          <caption class="sr-only">
            {{ t('Prévisualisation de {name}', { name: resource.title }) }}
          </caption>
          <thead>
            <tr>
              <th
                v-for="(col, index) in columns"
                :key="index"
                scope="col"
              >
                <BrandedButton
                  color="secondary-softer"
                  :icon="isSortedBy(col) && sortConfig && sortConfig.type == 'asc' ? RiArrowUpLine : RiArrowDownLine"
                  icon-right
                  size="xs"
                  @click="sortByField(col)"
                >
                  <!-- There is a weird bug with `sr-only`, I needed to add a relative parent to avoid full page x scrolling into the void…  -->
                  <span class="relative">
                    {{ col }}
                    <span class="sr-only">{{ sortConfig && sortConfig.type == 'desc' ? t("Trier par ordre croissant") : t("Trier par ordre décroissant") }}</span>
                  </span>
                </BrandedButton>
              </th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(row, rowIndex) in rows"
              :key="rowIndex"
            >
              <td
                v-for="(col, colIndex) in columns"
                :key="colIndex"
                class="cell-padding"
              >
                <div class="fr-grid-row fr-grid-row--middle fr-text--xs w-100 style-cell">
                  <div class="fr-my-auto">
                    {{ row[col] }}
                  </div>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <Pagination
        class="fr-mt-3w"
        :page="currentPage"
        :page-size="pageSize"
        :total-results="rowCount"
        @change="changePage"
      />
      <div class="fr-px-5v">
        {{ t("Dernière mise à jour de la prévisualisation : {date}", { date: lastUpdate }) }} —
        {{ t('{count} colonnes', columns.length) }} —
        {{ t('Lignes {count}', rowCount) }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from 'vue'
import { useI18n } from 'vue-i18n'
import { RiArrowDownLine, RiArrowUpLine, RiErrorWarningLine, RiExternalLinkFill } from '@remixicon/vue'
import Pagination from '../Pagination.vue'
import { getData, type SortConfig } from '../../functions/tabularApi'
import { useFormatDate } from '../../functions/dates'
import type { Resource } from '../../types/resources'
import { useComponentsConfig } from '../../config'
import BrandedButton from '../BrandedButton.vue'
import SimpleBanner from '../SimpleBanner.vue'
import franceSvg from './france.svg?raw'
import PreviewLoader from './PreviewLoader.vue'

const props = defineProps<{ resource: Resource }>()

const { t } = useI18n()
const { formatDate } = useFormatDate()

const rows = ref<Array<Record<string, unknown>>>([])
const columns = ref<Array<string>>([])
const loading = ref(true)
const hasError = ref(false)
const sortConfig = ref<SortConfig>(null)
const rowCount = ref(0)
const config = useComponentsConfig()
const pageSize = computed(() => config.tabularApiPageSize || 15)
const currentPage = ref(1)

/**
 * Check if the preview is sorted by the provided column
 */
function isSortedBy(col: string) {
  return col === sortConfig.value?.column
}

/**
 * Retrieve preview necessary infos
 */
async function getTableInfos(page: number, sortConfig?: SortConfig) {
  try {
    // Check that this function return wanted data
    const { data } = await getData(config, props.resource.id, page, sortConfig)
    if ('data' in data && data.data && data.data.length > 0) {
      // Update existing rows
      rows.value = data.data
      columns.value = Object.keys(data.data[0]).filter(item => item !== '__id')
      rowCount.value = data.meta.total
      currentPage.value = page
      loading.value = false
    }
    else {
      hasError.value = true
      loading.value = false
    }
  }
  catch {
    hasError.value = true
    loading.value = false
  }
};

/**
 * Change page
 */
function changePage(page: number) {
  getTableInfos(page, sortConfig.value)
}

/**
 * Sort by a specific column
 */
function sortByField(col: string) {
  if (sortConfig.value && sortConfig.value.column == col) {
    if (sortConfig.value.type == 'asc') {
      sortConfig.value.type = 'desc'
    }
    else {
      sortConfig.value.type = 'asc'
    }
  }
  else {
    if (!sortConfig.value) {
      sortConfig.value = {
        column: col,
        type: 'asc',
      }
    }
    else {
      sortConfig.value.column = col
      sortConfig.value.type = 'asc'
    }
  }
  currentPage.value = 1
  getTableInfos(currentPage.value, sortConfig.value)
};

const lastUpdate = computed(() => formatDate(props.resource.extras['analysis:parsing:finished_at']))

onMounted(() => {
  getTableInfos(currentPage.value)
})
</script>

<style scoped>
.style-cell {
  height: 3rem;
  overflow-y: auto;
}

.col-width {
  width: 20rem;
}

.cell-padding {
  padding: 0.5rem 0rem 0.5rem 1.2rem!important;
}

td {
  border-right: 1px solid #CECECE;
  border-bottom: 1px solid #CECECE;
}

th {
  border-right: 1px solid #CECECE;
}
</style>
