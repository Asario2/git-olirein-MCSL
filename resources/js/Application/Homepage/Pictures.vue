<template>
    <Layout>
        <MetaHeader :title="'Bilder - ' + ocont.slug" />
        <div @click="handleBodyClick">
        <div v-if="ocont.id" class="p-4 bg-layout-sun-200 dark:bg-layout-night-200">
        <hgroup>
            <h1 class="text-2xl font-bold">{{ decodeEntities(ocont.slug) }}</h1>
            <h4 v-html="ocont.description.replace('fx_year()', new Date().getFullYear()).replace(/\n/g, '<br />')"></h4>


        </hgroup>
       </div>
    <div>
            <div class="p-2 md:p-4" v-if="Array.isArray(entries.data) && entries.data.length === 0 && form.search">
                <alert type="warning">
                    Für den vorgegebenen Suchbegriff wurden keine Bilder gefunden.
                </alert>
            </div>

                            <button-group>
                                <input-icon-hyperlink
                                    v-if="createOn"
                                    :href="routeCreate"
                                    display_type="table"
                                >
                                    <template #icon>
                                        <icon-plus-circle
                                            class="button_icon"
                                        ></icon-plus-circle>
                                        Erstelle
                                    </template>
                                </input-icon-hyperlink>
                                <slot name="button"></slot>
                            </button-group>
                        </div>
                        <div class="flex justify-between items-center">
                            <search-filter
                            v-if="searchFilter"
                            v-model="form.search"
                            class="w-full"
                            @reset="reset"
                            />

                </div>

                <div id="gallery" v-for="item in entries.data" :key="item.id"
    class="w-full block max-w-sm gap-3 mx-auto sm:max-w-full group mb-4
        lg:grid lg:grid-cols-12 bg-layout-sun-100 dark:bg-layout-night-100
        border-2 border-layout-sun-300 dark:border-layout-night-300 p-4"

>

    <!-- Linke Spalte: Thumbnail -->
    <div :id="'st' + item.id" class="relative lg:col-span-3">
        <a
  :href="'/images/images/big/' + item.image_path"
  :data-pswp-width="item.img_x"
  :data-pswp-height="item.img_y"
>
 <ZoomImage
  :src="'/images/images/thumbs/' + item.image_path"
  :alt="item.title"
  :title="item.title"
  :width="300"
  :height="300"
  class="imgprev"
/>
</a>
    </div>

    <!-- Mittlere Spalte: Überschrift, Beschreibung und Kommentar-Slot -->
    <div class="p-6 space-y-2 lg:col-span-6 ml-6">
        <h2 class="text-xl font-semibold sm:text-2xl font-title whitespace-pre-line">
        <p v-html="item.headline"></p>
        </h2>

        <div class="text-layout-sun-700 dark:text-layout-night-700 whitespace-pre-line">
            <p v-html="item.message"></p>
        </div>


            <SocialButtons :postId="item.id" />
</div>


    <!-- Rechte Spalte: Kurzinfos, Bewertungs-Slot, Datum und optionale Kameraangabe -->
    <div class="p-6 space-y-2 lg:col-span-3">
        <div class="text-sm font-semibold text-layout-sun-800 dark:text-layout-night-800">
        <h3>Kurzinfos</h3>
        </div>
        <div v-if="item.Format">
        <b>Format:</b> {{ item.Format }}
        </div>
        <averageRating :postId="item.id" :av="parseFloat(ratings['original'][item.id]?.average) || 0" :tot="ratings['original'][item.id]?.total || 0"/>
        <!-- <span v-if="hasRight('delete', 'images') || hasRight('edit', 'images') " class="flex space-x-2">
        Edit Icon
        <span v-if="hasRight('edit', 'images') "
                class="inl"
                @click.prevent="editDataRow(item.id)">
            <icon-pencil class="w-6 h-6 cursor-pointer" v-tippy />
            <tippy>{{ editDescription }}</tippy>
        </span>

        <span v-if="hasRight('delete', 'images') "
                class="inl"
                @click="deleteDataRow(item.id)">
            <icon-trash class="w-6 h-6 cursor-pointer" v-tippy />
            <tippy>{{ deleteDescription }}</tippy>
        </span>
        </span> -->
        <editbtns :id="item.id" table="images" />
        <div class="text-xs text-layout-sun-600 dark:text-layout-night-600">
        <display-date :value="item.created_at" :time-on="false" />
        </div>

        <div v-if="item.camera" class="text-xs text-layout-sun-600 dark:text-layout-night-600">
        <IconCamera />&nbsp;&nbsp;{{ item.camera }}
        </div>
    </div>
</div>

<!-- Pagination -->
<div class="flex items-center justify-center flex-wrap mt-6 -mb-1 text-xs md:text-base bg-transparent text-layout-sun-700 dark:text-layout-night-700">
                    <template v-for="(link, index) in entries.links" :key="index">
                        <!-- Deaktivierte Links -->
                        <div
                            v-if="!link.url"
                            class="flex items-center px-3 py-0.5 mx-1 mb-1 rounded-md cursor-not-allowed"
                        >
                            <span v-html="link.label"></span>
                        </div>

                        <!-- Aktive Seite -->
                        <a
                            v-else-if="link.active"
                            :href="link.url"
                            class="flex items-center px-2.5 py-0.5 mx-1 mb-1 h-7 transition-colors duration-200 transform rounded-md border border-primary-sun-500 text-primary-sun-900 dark:border-primary-night-500 dark:text-primary-night-900 hover:bg-layout-sun-200 hover:text-layout-sun-800 dark:hover:bg-layout-night-200 dark:hover:text-layout-night-800 font-bold"
                        >
                            <span v-html="link.label"></span>
                        </a>

                        <!-- Normale Links -->
                        <a
                            v-else
                            :href="link.url"
                            class="flex items-center px-2.5 py-0.5 mx-1 mb-1 h-7 transition-colors duration-200 transform rounded-md border hover:bg-layout-sun-200 hover:text-layout-sun-800 dark:hover:bg-layout-night-200 dark:hover:text-layout-night-800"
                        >
                            <span v-html="link.label"></span>
                        </a>
                    </template>
                </div>
</div>

    </layout>
</template>

<script>
import Layout from "@/Application/Homepage/Shared/Layout.vue";
import MetaHeader from "@/Application/Homepage/Shared/MetaHeader.vue";
import ZoomImage from "@/Application/Components/Content/ZoomImage.vue";
import SocialButtons from "@/Application/Components/Social/socialButtons.vue";
import averageRating from "@/Application/Components/Social/averageratings.vue";
import editbtns from "@/Application/Components/Form/editbtns.vue";
import DisplayDate from "@/Application/Components/Content/DisplayDate.vue";
import IconCamera from "@/Application/Components/Icons/Camera.vue";
import PhotoSwipeLightbox from "photoswipe/lightbox";
import "photoswipe/style.css";
import he from "he";

export default {
  components: {
    Layout,
    MetaHeader,
    ZoomImage,
    SocialButtons,
    averageRating,
    editbtns,
    DisplayDate,
    IconCamera,
  },
  props: {
    entries: {
      type: [Array, Object],
      default: () => [],
    },
    ocont: {
      type: [Array, Object],
      default: () => [],
    },
    ratings: {
      type: Object,
      default: () => ({}),
    },
    createOn: {
      default: true,
    },
    searchFilter: {
      type: Boolean,
      default: true,
    },
    routeCreate: {
      type: String,
      default: '',
    },
    form: {
      type: Object,
      default: () => ({
        search: '',
      }),
    },
  },
  data() {
    return {
      lightbox: null,
      openIndex: null,
    };
  },
  methods: {
    decodeEntities(text) {
      if (text) {
        text = text.replace(/<br\s*\/?>/g, "\n");
        return he.decode(text);
      }
      return "";
    },
    handleBodyClick(event) {
      // Hier evtl. Kommentare schließen o.Ä.
    },
    /**
     * Prüft, ob der Link ein Admin/Tables-Link ist.
     * @param {string} href
     * @returns {boolean}
     */
    isAdminLink(href) {
      return href.startsWith("/admin/tables");
    },
    reset() {
      this.form.search = "";
    },
  },
  mounted() {
    const hash = window.location.hash;
    if (hash && hash.startsWith("#st")) {
      const id = hash.replace("#st", "");
      const index = this.entries.data.findIndex((item) => String(item.id) === id);

      if (index !== -1) {
        this.openIndex = index;

        this.$nextTick(() => {
          const el = document.getElementById(`st${id}`);
          if (el) {
            const y = el.getBoundingClientRect().top + window.pageYOffset - 134;
            window.scrollTo({ top: y, behavior: 'smooth' });
          }
        });
      }
    }

    this.lightbox = new PhotoSwipeLightbox({
      gallery: "#gallery",
      children: "a:not([href^='/admin/tables'])", // Admin-Links ausschließen
      pswpModule: () => import("photoswipe"),
      zoom: true,
      secondaryZoomLevel: 2,
      maxZoomLevel: 4,
      initialZoomLevel: "fit",
      wheelToZoom: true,
      barsSize: { top: 50, bottom: 50 },
      padding: { top: 30, bottom: 30, left: 30, right: 30 },
      showHideAnimationType: "zoom",
      galleryUID: "photoswipe-gallery",
    });

    this.lightbox.init();
  },
  beforeUnmount() {
    if (this.lightbox) {
      this.lightbox.destroy();
      this.lightbox = null;
    }
  },
};
</script>


  <style scoped>
  .gallery-container {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
  }

  .imgprev {
    cursor: zoom-in;
    max-width: 100%;
    height: auto;
    border-radius: 8px;
  }
  </style>
