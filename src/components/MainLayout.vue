<script setup>
import { ref } from 'vue';
import {
  Dialog,
  DialogPanel,
  TransitionChild,
  TransitionRoot,
} from '@headlessui/vue';
import { XMarkIcon } from '@heroicons/vue/24/outline';
import { PlusIcon } from '@heroicons/vue/20/solid';

const mobileFiltersOpen = ref(false);
</script>
<template>
  <div class="bg-white">
    <div>
      <!-- Mobile filter dialog -->
      <TransitionRoot
        as="template"
        :show="mobileFiltersOpen"
      >
        <Dialog
          as="div"
          class="relative z-40 lg:hidden"
          @close="mobileFiltersOpen = false"
        >
          <TransitionChild
            as="template"
            enter="transition-opacity ease-linear duration-300"
            enter-from="opacity-0"
            enter-to="opacity-100"
            leave="transition-opacity ease-linear duration-300"
            leave-from="opacity-100"
            leave-to="opacity-0"
          >
            <div class="fixed inset-0 bg-black bg-opacity-25" />
          </TransitionChild>
  
          <div class="fixed inset-0 z-40 flex">
            <TransitionChild
              as="template"
              enter="transition ease-in-out duration-300 transform"
              enter-from="translate-x-full"
              enter-to="translate-x-0"
              leave="transition ease-in-out duration-300 transform"
              leave-from="translate-x-0"
              leave-to="translate-x-full"
            >
              <DialogPanel class="relative ml-auto flex h-full w-full max-w-xs flex-col overflow-y-auto bg-white py-4 pb-6 shadow-xl">
                <div class="flex items-center justify-between px-4">
                  <h2 class="text-lg font-bold text-gray-500 uppercase">
                    Filtres
                  </h2>
                  <button
                    type="button"
                    class="-mr-2 flex h-10 w-10 items-center justify-center p-2 text-gray-400 hover:text-gray-500"
                    @click="mobileFiltersOpen = false"
                  >
                    <span class="sr-only">Fermer menu</span>
                    <XMarkIcon
                      class="h-6 w-6"
                      aria-hidden="true"
                    />
                  </button>
                </div>
                <div class="pb-4 pt-4 px-4 text-sm font-bold">
                  {{ marches.length }} marches trouvées
                </div>
                <!-- Filters -->
                <div
                  v-for="section in filters"
                  :key="section.name"
                  as="div"
                  class="border-t border-gray-200 pb-4 pt-4"
                >
                  <fieldset>
                    <legend class="w-full px-2">
                      <div class="flex w-full items-center justify-between p-2 text-gray-400 hover:text-gray-500">
                        <span class="text-sm font-bold text-gray-900 uppercase">{{ section.name }}</span>
                      </div>
                    </legend>
                    <div
                      v-if="data_loaded"
                    >
                      <div class="px-4 pb-2">
                        <div class="space-y-3">
                          <button
                            v-for="option in section.options"
                            :key="option"
                            class="flex items-center"
                            @click="filterCategory(section.id, option)"
                          >
                            {{ option }} <span class="text-gray-500">({{ getCountCategory(section.id, option) }})</span>
                          </button>
                          <button
                            v-if="isProvinceFiltered"
                            class="rounded-full bg-blue-600 px-2.5 py-1 text-xs font-semibold text-white shadow-sm hover:bg-blue-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600"
                            @click="resetFilter"
                          >
                            Retirer filtre
                          </button>
                        </div>
                      </div>
                  
                      <legend class="w-full px-2">
                        <div class="flex w-full items-center justify-between p-2 text-gray-400 hover:text-gray-500 border-t border-gray-200 ">
                          <span class="text-sm font-bold text-gray-900 uppercase">Quand</span>
                        </div>
                      </legend>
                      <div class="space-y-3 px-4 pb-2">
                        {{ weekDiffForHumans(start_date, end_date) }} <br>
                        <span class="text-gray-800 text-sm">Du {{ toFrenchDate(start_date) }} au {{ toFrenchDate(end_date) }}
                        </span>

                        <div class="pt-4">
                          <span class="isolate inline-flex rounded-md shadow-sm">
                            <button
                              type="button"
                              class="relative inline-flex items-center rounded-l-md bg-white px-2 py-2 text-gray-600 ring-1 ring-inset ring-gray-300 hover:bg-gray-50 focus:z-10"
                              @click="previousMarches"
                            >
                              <ChevronLeftIcon
                                class="h-5 w-5"
                                aria-hidden="true"
                              />
                              précédente
                            </button>
                            <button
                              type="button"
                              class="relative -ml-px inline-flex items-center rounded-r-md bg-white px-2 py-2 text-gray-600 ring-1 ring-inset ring-gray-300 hover:bg-gray-50 focus:z-10"
                              @click="nextMarches"
                            >
                              suivante
                              <ChevronRightIcon
                                class="h-5 w-5"
                                aria-hidden="true"
                              />
                            </button>
                          </span>
                        </div>
                      </div>
                    </div>
                  </fieldset>
                </div>
              </DialogPanel>
            </TransitionChild>
          </div>
        </Dialog>
      </TransitionRoot>
  
      <main class="mx-auto max-w-2xl px-4 py-8 sm:px-6 sm:py-24 lg:max-w-7xl lg:px-8">
        <div class="border-b border-gray-200 pb-10">
          <!-- logo -->
          <div class="flex items-center justify-center">
            <img
              src="https://raw.githubusercontent.com/data-cfwb/charte-graphique/main/pastilles_PNG_et_SVG_24px/pastille_Adeps24.svg"
              alt="Fédération Wallonie-Bruxelles"
              width="48"
              class="px-1 sm:px-1"
            >
            <h1 class="text-4xl font-bold tracking-tight text-gray-900">
              Les marches ADEPS
            </h1>
          </div>
          <!-- hide on small -->
          <p class="mt-4 text-base text-gray-500 max-md:hidden">
            Les marches ADEPS sont des marches organisées par la Fédération Wallonie-Bruxelles pour promouvoir la pratique de la marche en tant qu'activité physique. Vous trouverez ci-dessous celles organisées du <span class="font-semibold">{{ toFrenchDate(start_date) }}</span> au <span class="font-semibold">{{ toFrenchDate(end_date) }}</span>.
          </p>
  
          <div class="pt-12 lg:grid lg:grid-cols-3 lg:gap-x-8 xl:grid-cols-4">
            <aside>
              <h2 class="sr-only">
                Filtres
              </h2>
  
              <button
                type="button"
                class="inline-flex items-center lg:hidden"
                @click="mobileFiltersOpen = true"
              >
                <span class="text-sm font-medium text-gray-700 uppercase">Filtres</span>
                <PlusIcon
                  class="ml-1 h-5 w-5 flex-shrink-0 text-gray-400"
                  aria-hidden="true"
                />
              </button>
  
              <div class="hidden lg:block">
                <div
                  class="py-6 pt-10"
                >
                  <div class="block text-md font-bold leading-6 text-gray-900 uppercase">
                    <CalendarIcon class="h-6 w-6 text-blue-500 inline-flex items-baseline" />
                    Semaine
                  </div>
                  <div>
                    {{ weekDiffForHumans(start_date, end_date) }}
                  </div>
                  <div class="isolate inline-flex rounded-md shadow-sm pt-6">
                    <button
                      type="button"
                      class="relative inline-flex items-center rounded-l-md bg-white px-2 py-2 text-gray-600 ring-1 ring-inset ring-gray-300 hover:bg-gray-50 focus:z-10"
                      @click="previousMarches"
                    >
                      <ChevronLeftIcon
                        class="h-5 w-5"
                        aria-hidden="true"
                      />
                      précédente
                    </button>
                    <button
                      class="relative -ml-px inline-flex items-center bg-white px-3 py-2 text-sm font-semibold text-gray-900 ring-1 ring-inset ring-gray-300 hover:bg-gray-50 focus:z-10"
                      alt="Cette semaine"
                      @click="refreshMarches()"
                    >
                      <ArrowPathRoundedSquareIcon
                        class="h-6 w-6 text-blue-500"
                        aria-hidden="true"
                      />
                    </button>
                    <button
                      type="button"
                      class="relative -ml-px inline-flex items-center rounded-r-md bg-white px-2 py-2 text-gray-600 ring-1 ring-inset ring-gray-300 hover:bg-gray-50 focus:z-10"
                      @click="nextMarches"
                    >
                      suivante
                      <ChevronRightIcon
                        class="h-5 w-5"
                        aria-hidden="true"
                      />
                    </button>
                  </div>
                </div>
                
                <div
                  v-for="(section, sectionIdx) in filters"
                  :key="section.name"
                  :class="sectionIdx === 0 ? null : 'pt-10'"
                >
                  <fieldset>
                    <legend class="block text-md leading-6 font-bold text-gray-900 uppercase">
                      <MapPinIcon class="h-6 w-6 text-blue-500 inline-flex items-baseline" />
                      {{ section.name }}
                    </legend>
                    <div class="space-y-3 pt-2">
                      <button
                        v-for="option in section.options"
                        :key="option"
                        class="flex items-center hover:text-blue-900"
                        @click="filterCategory(section.id, option)"
                      >
                        {{ option }} <span class="text-gray-500">({{ getCountCategory(section.id, option) }})</span>
                      </button>
                      <button
                        v-if="isProvinceFiltered"
                        class="rounded-full bg-blue-600 px-2.5 py-1 text-xs font-semibold text-white shadow-sm hover:bg-blue-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600"
                        @click="resetFilter"
                      >
                        Retirer filtre
                      </button>
                    </div>
                  </fieldset>
                </div>
              </div>
            </aside>
  
            <!-- Marche table -->
            <div class="mt-6 lg:col-span-2 lg:mt-0 xl:col-span-3">
              <!-- Your content -->
              <div>
                <LoadingFwb v-if="!data_loaded" />
                <div v-else>
                  <div class="px-4 sm:px-6 lg:px-8">
                    <div class="sm:flex sm:items-center">
                      <div class="sm:flex-auto" />
                      <div class="mt-4 sm:ml-16 sm:mt-0 sm:flex-none">
                        <button
                          v-if="!seeMap"
                          type="button"
                          class="relative inline-flex items-center block rounded-md bg-green-600 px-3 py-2 text-center text-sm font-semibold text-white shadow-sm hover:bg-green-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-green-600"                     
                          @click="seeMap = !seeMap"
                        >
                          <MapPinIcon class="h-6 w-6 text-white" />
                          Voir sur une carte
                        </button>
                        <button
                          v-if="seeMap"
                          type="button"
                          class="relative inline-flex items-center block block rounded-md bg-blue-600 px-3 py-2 text-center text-sm font-semibold text-white shadow-sm hover:bg-blue-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600"
                          @click="seeMap = !seeMap"
                        >
                          <ListBulletIcon class="h-6 w-6 text-white" />
                          Liste des marches
                        </button>
                      </div>
                    </div>
                    <div class="mt-8 flow-root">
                      <div class="-mx-4 -my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
                        <div class="inline-block min-w-full py-2 align-middle sm:px-6 lg:px-8">
                          <div
                            v-if="seeMap" 
                            class="overflow-hidden border-t border-gray-200 sm:rounded-lg"
                          >
                            <MapComponent
                              :marches="marches"
                              :selected-marche="selected_marche"
                            />
                          </div>
                          <ListMarches
                            v-if="!seeMap"
                            :marches="marches"
                            @selected-marche="selectMarche"
                          />
                        </div>
                      </div>
                    </div>
                  </div>
                </div>  
              </div>
            </div>
          </div>
        </div>
      </main>
    </div>
    <FooterModule />
  </div>
</template>
  
<script>
import axios from 'axios';
import MapComponent from '@/components/MapComponent.vue';
import FooterModule from '@/components/partials/FooterModule.vue';
import ListMarches from '@/components/ListMarches.vue';
import { MapPinIcon, CalendarIcon, ArrowPathRoundedSquareIcon, ListBulletIcon } from '@heroicons/vue/24/outline';
import { ChevronLeftIcon, ChevronRightIcon } from '@heroicons/vue/20/solid';
export default {
  components: {
    MapComponent,
    ListMarches,
    MapPinIcon,
    CalendarIcon,
    ArrowPathRoundedSquareIcon,
    ListBulletIcon,
    FooterModule
  },
  data() {
    return {
      title: 'Les marches ADEPS',
      filters: [ 
        {
          id: 'province',
          name: 'Provinces',
          options: [],
        }
      ],
      current_date: new Date(),
      start_date: '',
      end_date: '',
      selected_marche: {},
      original_marches: {},
      isProvinceFiltered: false,
      marches: {},
      data_loaded: false,
      seeMap: false,
    };
  },
  computed: {
    addProperties() {
      return this.marches.map(marche => {
        marche.diffDays = Math.floor((new Date(marche.date) - new Date()) / (1000 * 60 * 60 * 24)) + 1;
        // custom function to get the difference between marche.date and today
        marche.diffFromTodayInFrench = this.getDiffFromTodayInFrench(marche.diffDays);

        marche.isPast = marche.diffDays < 0;
        // revert negative days to 0
        marche.frenchDate = this.toFrenchDate(marche.date);
        marche.frenchDayOfWeek = new Date(marche.date).toLocaleDateString('fr-BE', { weekday: 'long' });     
        marche.latLong = [parseFloat(marche.latitude), parseFloat(marche.longitude)];
        
        marche.parcours = [];
        // data is not consistent with real parcours, so we manually add the parcours
        marche.parcours.push({ label: '5km', value: 'Oui' });
        marche.parcours.push({ label: '10km', value: 'Oui' });
        marche.parcours.push({ label: '15km', value: marche['15km'] });
        marche.parcours.push({ label: '20km', value: 'Oui' });

        marche.services = [];
  
        marche.services.push({ label: 'PMR 🧑‍🦽', value: marche['pmr'] });
        marche.services.push({ label: 'Poussettes 🍼', value: marche['poussettes'] });
        marche.services.push({ label: 'Orientation 🧭', value: marche['orientation'] });
        marche.services.push({ label: 'Balade guidée 🧑‍🌾', value: marche['balade_guidee'] });
        marche.services.push({ label: 'VTT 🚵', value: marche['vtt'] });
        marche.services.push({ label: 'Ravitaillement 🧃', value: marche['ravitaillement'] });
        marche.services.push({ label: 'BeWapp ♻️', value: marche['bewapp'] });
        marche.services.push({ label: 'Adeps Santé 🏃', value: marche['adep_sante'] });
        for (let i = marche.services.length - 1; i >= 0; i--) {
          if (marche.services[i].value === 'Non') {
            marche.services.splice(i, 1);
          }
        }
        // trim marche.localite and marche.entite
        marche.localite = marche.localite.replace(/\n/g, ' ').trim();
        marche.entite = marche.entite.replace(/\n/g, ' ').trim();

        marche.address = marche.lieu_de_rendez_vous + ', ' + marche.localite + ', ' + marche.province;
        // 20240118T070000
        marche.icsStartDate = new Date(marche.date).toISOString().slice(0, 10).replace(/-/g, '') + 'T080000';
        marche.icsEndDate = new Date(marche.date).toISOString().slice(0, 10).replace(/-/g, '') + 'T160000';

        marche.fullName = marche.prenom + ' ' + marche.nom;

        return marche;
      });
    },
  },
  created() {
    this.start_date = this.diffDateIso(new Date(), 0);
    this.end_date = this.diffDateIso(this.start_date, 6);
    this.getMarches();
  },
  methods: {
    weekDiffForHumans(start, end) {
      const start_diff_days = Math.floor((new Date(start) - new Date()) / (1000 * 60 * 60 * 24)) + 1;
      const end_diff_days = Math.floor((new Date(end) - new Date()) / (1000 * 60 * 60 * 24)) + 1;
      if (start_diff_days >= 0 && end_diff_days <= 6) {
        return 'Cette semaine';
      }
      else if (start_diff_days > 6 && end_diff_days <= 13) {
        return 'La semaine prochaine';
      }
      else if (start_diff_days > 13) {
        // count weeks
        const weeks = Math.floor(start_diff_days / 7);
        return 'Dans ' + weeks + ' semaines';
      }
      else if (end_diff_days < 0 && start_diff_days >= -7) {
        return 'La semaine passée';
      }
      else if (start_diff_days < -7) {
        // count weeks
        const weeks = Math.floor(start_diff_days / 7);
        return 'Il y a ' + Math.abs(weeks) + ' semaines';
      }
      else {
        return 'Du ' + this.toFrenchDate(start) + ' au ' + this.toFrenchDate(end);
      }
    },
    selectMarche(marche) {
      // show map with selected marche
      this.seeMap = true;
      this.selected_marche = marche;
      console.log(marche);
    },
    toFrenchDate(date) {
      return new Date(date).toLocaleDateString('fr-BE', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
    },
    getCountCategory(category, item) {
      return this.marches.filter(marche => marche[category] === item).length;
    },
    defineFilters() {
      for (let i = 0; i < this.filters.length; i++) {
        this.filters[i].options = [...new Set(this.marches.map(marche => marche[this.filters[i].id]))].sort();
      }
    },
    getDiffFromTodayInFrench(diffDays) {
      if (diffDays === 0) {
        return 'Aujourd\'hui';
      }
      else if (diffDays === 1) {
        return 'Demain';
      }
      else if (diffDays === -1) {
        return 'Hier';
      }
      else if (diffDays > 1) {
        return 'dans ' + diffDays + ' jours';
      }
      else if (diffDays < -1) {
        return 'il y a ' + Math.abs(diffDays) + ' jours';
      }
    },
    diffDateIso(date, diffInDays) {
      return new Date(new Date(date).getTime() + diffInDays * 24 * 60 * 60 * 1000).toISOString().slice(0, 10);
    },
    nextMarches() {
      this.start_date = this.diffDateIso(this.start_date, 7);
      this.end_date = this.diffDateIso(this.end_date, 7);
      this.getMarches();
    },
    previousMarches() {
      this.start_date = this.diffDateIso(this.start_date, -7);
      this.end_date = this.diffDateIso(this.end_date, -7);
      this.getMarches();
    },
    refreshMarches() {
      this.start_date = this.diffDateIso(new Date(), 0);
      this.end_date = this.diffDateIso(this.start_date, 6);
      this.getMarches();
    },
   
    filterCategory(category, item) {
      if (category === 'province') {
        this.isProvinceFiltered = true;
        this.marches = this.marches.filter(marche => {
          return marche.province === item;
        });
      }
      else if (category === 'date') {
        this.marches = this.marches.filter(marche => {
          return marche.date === item;
        });
      }
      this.defineFilters();
    },
  
    orderByStatusDateAndProvince() {
      this.marches.sort((a, b) => {
        return new Date(a.date) - new Date(b.date);
      });
      this.marches.sort((a, b) => {
        return a.province.localeCompare(b.province);
      });
      // if status is not OK, put it at the end
      this.marches.sort((a) => {
        return a.statut === 'OK' ? -1 : 1;
      });
    },
    resetFilter() {
      this.isProvinceFiltered = false;
      this.marches = this.original_marches;
      this.defineFilters();
    },
    getMarches() {
      this.isProvinceFiltered = false;
      this.data_loaded = false;
      axios.get('https://www.odwb.be/api/explore/v2.1/catalog/datasets/points-verts-de-ladeps/exports/json?lang=fr&qv1=(date%3A[' + this.start_date + '%20TO%20' + this.end_date + '])&timezone=Europe%2FBrussels')
        .then(response => {
          this.original_marches = response.data;
          this.marches = response.data;
          this.addProperties;
          this.orderByStatusDateAndProvince();
          this.defineFilters();
          // console.log(this.marches);
          this.data_loaded = true;
        })
        .catch(error => {
          console.log(error);
        });
    },
  
  },
};

</script>
