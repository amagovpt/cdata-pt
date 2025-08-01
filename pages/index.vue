<template>
  <div>
    <section class="py-8 sm:py-[200px] bg-cover bg-[url(''),radial-gradient(rgba(255,255,255,0.8),rgba(217,217,217,0)),url('/nuxt_images/homepage/hero.png')]">
      <div class="max-w-[800px] mx-auto px-6 flex flex-col items-center space-y-8">
        <div class="w-full sm:w-auto flex flex-col sm:flex-row gap-5 items-center">
          <CdataLink
            v-if="lastPost"
            class="flex items-center space-x-2.5 w-full sm:max-w-[340px] px-3 py-0.5 fr-raw-link text-primary border  border-primary rounded-lg bg-white"
            :to="`/posts/${lastPost.slug}`"
          >
            <RiBardLine class="size-4 shrink-0" />
            <span class="truncate"><span class="font-bold">{{ $t('Actualités') }}</span><span>&nbsp;: {{ lastPost.name }}</span></span>
          </CdataLink>
          <CdataLink
            v-if="config.public.homepageRightNow"
            class="flex items-center space-x-2.5 w-full sm:max-w-[340px] px-3 py-0.5 fr-raw-link text-primary border  border-primary rounded-lg bg-white"
            :to="config.public.homepageRightNow.url"
          >
            <RiBardLine class="size-4 shrink-0" />
            <span class="truncate"><span class="font-bold">{{ $t('En ce moment') }}</span><span>&nbsp;: {{ config.public.homepageRightNow.title }}</span></span>
          </CdataLink>
        </div>
        <div class="space-y-4">
          <h1 class="text-6xl font-extrabold text-primary text-center">
            {{ $t('La plateforme des données publiques françaises') }}
          </h1>
          <p class="font-spectral italic text-2xl text-center text-primary">
            {{ $t('Utilisez, partagez et améliorez les données publiques') }}
          </p>
        </div>
        <div class="w-full flex flex-col justify-center sm:flex-row items-center gap-2.5">
          <BrandedButton
            href="/datasets"
            class="w-full sm:w-auto"
          >
            {{ $t('Découvrez les jeux de données') }}
          </BrandedButton>
          <BrandedButton
            href="/reuses"
            color="primary-soft"
            class="w-full sm:w-auto"
          >
            {{ $t('Explorez les réutilisations de données') }}
          </BrandedButton>
        </div>
      </div>
    </section>
    <section class="grid grid-cols-1 sm:grid-cols-2">
      <div class="px-6 flex justify-center py-8 sm:py-24">
        <div class="w-full max-w-lg space-y-8">
          <div class="space-y-2">
            <nuxt-img
              src="/illustrations/dataset.svg"
              class="h-16 grayscale"
            />
            <h2 class="text-3xl text-gray-title font-extrabold">
              {{ $t('Le catalogue des données françaises') }}
            </h2>
            <p>{{ $t('Découvrez des données sur tous les sujets, produites par l’administration et la société civile.') }}</p>
          </div>

          <div class="flex flex-col sm:flex-row sm:justify-between gap-4">
            <div
              v-for="{ label, value } in siteMetrics"
              :key="label"
            >
              <div class="text-2xl font-extrabold">
                {{ summarize(value) }}
              </div>
              <div class="uppercase text-sm">
                {{ label }}
              </div>
            </div>
          </div>

          <div class="space-y-2.5">
            <p class="uppercase font-bold text-gray-low">
              {{ $t('Ils publient des données sur {name}', { name: config.public.title }) }}
            </p>
            <div class="grid grid-cols-2 sm:grid-cols-4 justify-between">
              <nuxt-img
                v-for="{ label, name } in [
                  { label: 'Santé publique France', name: 'spf' },
                  { label: 'CNIL', name: 'cnil' },
                  { label: 'INSEE', name: 'insee' },
                  { label: `Ministère de l'Éducation Nationale et de la Jeunesse`, name: 'minedu' },
                ]"
                :key="name"
                :src="`/nuxt_images/organizations/${name}.png`"
                :srcset="`/nuxt_images/organizations/${name}.png, /nuxt_images/organizations/${name}@2x.png 2x, /nuxt_images/organizations/${name}@3x.png 3x`"
                :alt="label"
                class="size-20 grayscale opacity-80 object-contain"
              />
            </div>
          </div>

          <div class="flex flex-col sm:flex-row items-center gap-4">
            <BrandedButton
              href="/datasets"
              class="w-full sm:w-auto"
            >
              {{ $t('Voir les données') }}
            </BrandedButton>
            <BrandedButton
              :href="config.public.homepagePublishDatasetOnboarding"
              color="primary-soft"
              class="w-full sm:w-auto"
            >
              {{ $t('Comment publier des données ?') }}
            </BrandedButton>
          </div>
        </div>
      </div>
      <div class="hidden sm:flex px-6 justify-center pt-8 sm:pt-10 overflow-hidden bg-gray-some">
        <figure class="w-full max-w-lg flex flex-col items-center">
          <figcaption class="uppercase font-bold text-gray-low mb-6">
            {{ $t('Jeux de données') }}
          </figcaption>
          <nuxt-img
            src="/nuxt_images/homepage/datasets.png"
            srcset="/nuxt_images/homepage/datasets.png, /nuxt_images/homepage/datasets@2x.png 2x, /nuxt_images/homepage/datasets@3x.png 3x"
            class="w-full sm:-mb-16"
          />
        </figure>
      </div>
      <div class="hidden sm:flex px-6 justify-center py-8 sm:pt-10 sm:pb-20 bg-gray-some">
        <figure class="w-full max-w-lg flex flex-col items-center">
          <figcaption class="uppercase font-bold text-gray-low mb-6">
            {{ $t('Réutilisations') }}
          </figcaption>
          <nuxt-img
            src="/nuxt_images/homepage/reuses.png"
            srcset="/nuxt_images/homepage/reuses.png, /nuxt_images/homepage/reuses@2x.png 2x, /nuxt_images/homepage/reuses@3x.png 3x"
            class="w-full"
          />
        </figure>
      </div>
      <div class="px-6 flex justify-center py-8 sm:py-24">
        <div class="w-full max-w-lg space-y-8">
          <div class="space-y-2">
            <nuxt-img
              src="/illustrations/discussion.svg"
              class="h-16 grayscale"
            />
            <h2 class="text-3xl text-gray-title font-extrabold">
              {{ $t('Une communauté dynamique et engagée') }}
            </h2>
            <p>{{ $t('Partagez votre usage des données et échangez entre producteurs et réutilisateurs de données.') }}</p>
          </div>

          <div class="flex flex-col sm:flex-row sm:justify-between gap-4">
            <div
              v-for="{ label, value } in reusesMetrics"
              :key="label"
            >
              <div class="text-2xl font-extrabold">
                {{ summarize(value) }}
              </div>
              <div class="uppercase text-sm">
                {{ label }}
              </div>
            </div>
          </div>

          <div class="space-y-2.5">
            <p class="uppercase font-bold text-gray-low">
              {{ $t('Ils réutilisent les données de {name}', { name: config.public.title }) }}
            </p>
            <div class="grid grid-cols-2 sm:grid-cols-4 justify-between">
              <nuxt-img
                v-for="{ label, name } in [
                  { label: 'Le Monde', name: 'lemonde' },
                  { label: 'Google Maps', name: 'googlemaps' },
                  { label: 'Libération', name: 'liberation' },
                  { label: 'Fondation Wikimedia', name: 'wikimedia' },
                ]"
                :key="name"
                :src="`/nuxt_images/organizations/${name}.png`"
                :srcset="`/nuxt_images/organizations/${name}.png, /nuxt_images/organizations/${name}@2x.png 2x, /nuxt_images/organizations/${name}@3x.png 3x`"
                :alt="label"
                class="size-20 grayscale opacity-80 object-contain"
              />
            </div>
          </div>

          <div class="flex flex-col sm:flex-row items-center gap-4">
            <BrandedButton
              href="/reuses"
              class="w-full sm:w-auto"
            >
              {{ $t('Voir les réutilisations') }}
            </BrandedButton>
            <BrandedButton
              :href="config.public.homepagePublishReuseOnboarding"
              color="primary-soft"
              class="w-full sm:w-auto"
            >
              {{ $t('Comment réutiliser des données ?') }}
            </BrandedButton>
          </div>
        </div>
      </div>
    </section>
    <section class="bg-gray-dark text-white">
      <div class="max-w-7xl mx-auto px-6 py-16 space-y-8">
        <div>
          <h2 class="text-3xl font-normal mb-0">
            {{ $t('La mission {site}', { site: config.public.title }) }}
          </h2>
          <h3 class="text-3xl mb-0 font-extrabold">
            {{ $t('Simplifier l’accès aux données publiques') }}
          </h3>
        </div>
        <div class="space-y-6 max-w-4xl">
          <p class="text-3xl font-light text-gray-silver">
            {{ $t('L’ouverture et la circulation des données publiques renforcent la transparence, améliorent l’action publique et permettent la création de nouveaux services.') }}
          </p>
          <p class="text-3xl font-light text-gray-silver">
            {{ $t('{name} assure la mise à disposition de ces informations en organisant leur diffusion et leur exploitation.', { name: config.public.title }) }}
          </p>
        </div>
        <div>
          <h4 class="text-base uppercase font-bold text-gray-low">
            {{ $t('Nos objectifs') }}
          </h4>
          <div class="grid grid-cols-1 sm:grid-cols-3 space-y-6 sm:space-y-0">
            <div
              v-for="{ label, icon } in [
                { label: $t('Faciliter la découvrabilité des données'), icon: RiSearchLine },
                { label: $t('Améliorer la qualité  des données'), icon: RiVipDiamondLine },
                { label: $t('Encourager la réutilisation des données'), icon: RiLineChartLine },
              ]"
              :key="label"
              class="px-4 sm:px-8 border-l border-white space-y-2"
            >
              <component
                :is="icon"
                class="size-6"
              />
              <p class="text-2xl font-bold mb-0">
                {{ label }}
              </p>
            </div>
          </div>
        </div>
        <div class="flex justify-center my-16">
          <BrandedButton
            :href="config.public.homepageAboutUs"
            color="secondary"
            class="w-full sm:w-auto"
          >
            {{ $t('En savoir plus sur {name}', { name: config.public.title }) }}
          </BrandedButton>
        </div>
        <div class="space-y-8">
          <h3 class="text-3xl font-extrabold">
            {{ $t('Produire de l’information à partir des données') }}
          </h3>
          <div>
            <h4 class="text-base uppercase font-bold text-gray-low">
              {{ $t('Nos explorations de données') }}
            </h4>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-5">
              <div
                v-for="exploration in [
                  { title: $t('Explorateur de données de valeurs foncières'), description: $t(`Suivez l'évolution des prix de l'immobilier et trouver le prix des ventes immobilières.`), url: 'https://explore.data.gouv.fr/fr/immobilier', image: 'dvf' },
                  { title: $t('Annuaire des Entreprises'), description: $t('Vérifiez les informations légales publiques des entreprises, associations et services publics en France.'), url: 'https://annuaire-entreprises.data.gouv.fr/', image: 'annuaire' },
                ]"
                :key="exploration.url"
                class="bg-white"
              >
                <nuxt-img
                  :src="`/nuxt_images/homepage/${exploration.image}.png`"
                  :srcset="`/nuxt_images/homepage/${exploration.image}.png, /nuxt_images/homepage/${exploration.image}@2x.png 2x`"
                  class="w-full h-[300px] object-cover"
                />
                <div class="p-4 space-y-2.5">
                  <p class="text-sm uppercase font-bold text-gray-low">
                    {{ exploration.title }}
                  </p>
                  <p class="text-base text-gray-title font-bold">
                    {{ exploration.description }}
                  </p>
                  <div class="text-right">
                    <CdataLink
                      :to="exploration.url"
                      external
                      class="inline-flex items-center text-primary space-x-1"
                    >
                      <span>{{ $t('En savoir plus') }}</span>
                      <RiArrowRightLine class="size-3" />
                    </CdataLink>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="flex justify-center my-16">
          <BrandedButton
            :href="config.public.homepageExplore"
            color="secondary"
            external
            class="w-full sm:w-auto"
          >
            {{ $t('Découvrez nos explorations de données') }}
          </BrandedButton>
        </div>
        <div class="space-y-8">
          <h3 class="text-3xl font-extrabold">
            {{ $t('Fédérer un écosystème') }}
          </h3>
          <div>
            <h4 class="text-base uppercase font-bold text-gray-low">
              {{ $t('Nos plateformes thématiques') }}
            </h4>
            <div class="grid grid-cols-1 sm:grid-cols-3 gap-8">
              <div
                v-for="platform in [
                  { name: 'ecologie', url: 'https://ecologie.data.gouv.fr' },
                  { name: 'transport', url: 'https://transport.data.gouv.fr' },
                  { name: 'météo', url: 'https://meteo.data.gouv.fr' },
                ]"
                :key="platform.name"
                class="relative bg-gray-disabled p-4 space-y-2 hover:bg-gray-plain"
              >
                <h5 class="text-xl">
                  <CdataLink
                    :to="platform.url"
                    external
                    class="fr-raw-link"
                  >
                    <span class="font-spectral italic text-[1.4rem]">{{ platform.name }}</span><span class="font-bold">.data.gouv.</span><span class="font-spectral italic text-[1.4rem]">fr</span>
                    <div class="absolute inset-0" />
                  </CdataLink>
                </h5>
                <i18n-t
                  v-if="platform.name === 'ecologie'"
                  tag="p"
                  class="mb-0 text-gray-silver"
                  keypath="Les données de la transition écologique portées par le {name}."
                  scope="global"
                >
                  <template #name>
                    <strong>Ministère de la Transition écologique et de la Cohésion des territoires</strong>
                  </template>
                </i18n-t>
                <i18n-t
                  v-if="platform.name === 'transport'"
                  tag="p"
                  class="mb-0 text-gray-silver"
                  keypath="Les données de mobilité en partenariat avec la {name}."
                  scope="global"
                >
                  <template #name>
                    <strong>Direction Générale des Infrastructures, des Transports et des Mobilités</strong>
                  </template>
                </i18n-t>
                <i18n-t
                  v-if="platform.name === 'météo'"
                  tag="p"
                  class="mb-0 text-gray-silver"
                  keypath="Les données publiques relatives à la météorologie et à la climatologie produites par {name}."
                  scope="global"
                >
                  <template #name>
                    <strong>Météo-France</strong>
                  </template>
                </i18n-t>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <section v-if="lastPost">
      <div class="max-w-7xl mx-auto px-6 py-16 space-y-8">
        <h2 class="text-base uppercase font-bold text-gray-low mb-6">
          {{ $t("L'actualité {name}", { name: config.public.title }) }}
        </h2>
        <div class="grid grid-cols-1 sm:grid-cols-12 gap-8">
          <img
            v-if="lastPost.image"
            :src="lastPost.image"
            class="sm:col-span-5"
          >
          <div class="sm:col-span-7 flex flex-col justify-center space-y-8">
            <div class="space-y-2">
              <h3 class="text-gray-title font-extrabold text-3xl">
                {{ lastPost.name }}
              </h3>
              <p class="text-mention-grey text-sm">
                {{ $t('Publié le {date}', { date: formatDate(lastPost.published) }) }}
              </p>
              <p class="text-gray-plain mb-0">
                {{ lastPost.headline }}
              </p>
            </div>
            <div class="flex flex-col sm:flex-row items-center gap-2">
              <BrandedButton
                :href="lastPost.page"
                class="w-full sm:w-auto"
              >
                {{ $t("Consulter l'article") }}
              </BrandedButton>
              <BrandedButton
                color="primary-soft"
                href="/posts"
                class="w-full sm:w-auto"
              >
                {{ $t('Voir toutes les actualités') }}
              </BrandedButton>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import { BrandedButton, summarize, useFormatDate, type Site } from '@datagouv/components-next'
import { RiArrowRightLine, RiBardLine, RiLineChartLine, RiSearchLine, RiVipDiamondLine } from '@remixicon/vue'
import type { Post } from '~/types/posts'
import type { PaginatedArray } from '~/types/types'

useSeoMeta({ title: 'Accueil — data.gouv.fr' })

const config = useRuntimeConfig()
const { t } = useI18n()
const { formatDate } = useFormatDate()

const { data: posts } = await useAPI<PaginatedArray<Post>>('/api/1/posts')
const { data: site } = await useAPI<Site>('/api/1/site')

const lastPost = computed(() => {
  if (!posts.value || !posts.value.data.length) return null
  return posts.value.data[0]
})

const siteMetrics = computed(() => {
  return [
    { label: t('Jeux de données et API'), value: site.value ? site.value.metrics.datasets + site.value.metrics.dataservices : 0 },
    { label: t('Fichiers'), value: site.value ? site.value.metrics.resources : 0 },
    { label: t('Organisations'), value: site.value ? site.value.metrics.organizations : 0 },
  ]
})

const reusesMetrics = computed(() => {
  return [
    { label: t('Réutilisations'), value: site.value ? site.value.metrics.reuses : 0 },
    { label: t('Utilisateurs'), value: site.value ? site.value.metrics.users : 0 },
    { label: t('Discussions'), value: site.value ? site.value.metrics.discussions : 0 },
  ]
})
</script>
