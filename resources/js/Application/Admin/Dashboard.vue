<template>
    <layout title="Admin-Dashboard">
        <template #header>
            <breadcrumb
                :application-name="$page.props.applications.app_admin_name"
                :start-page="true"
            ></breadcrumb>
        </template>

        <div
            class="w-full bg-layout-sun-0 dark:bg-layout-night-0 grid grid-cols-1 md:grid-cols-2 gap-2 lg:gap-3 p-6 lg:p-4"
        >
            <!-- Blogartikel -->
            <navigation-card v-if="canView('blogs')"
                class="navigation_card p-4 rounded-md bg-layout-sun-100 dark:bg-layout-night-100"
                title="Blogartikel"
                :routeName="route('admin.tables.show','blogs')"
                linkName="Liste der Blogartikel"
                :routeName2="route('admin.tables.create', 'blogs')"
                linkName2="Neuer Blogartikel"
                :withIcon="true"
                icon="IconBook"
            >
                <template #description>
                                Hier findest du eine Liste aller Blogartikel.
                </template>
            </navigation-card>
            <!-- Bilder -->
            <navigation-card v-if="canView('images')"
                class="navigation_card p-4 rounded-md bg-layout-sun-100 dark:bg-layout-night-100"
                title="Bilder"
                :routeName="route('admin.tables.show','images')"
                linkName="Überblick Bilder"
                :routeName2="route('admin.tables.create', 'images')"
                linkName2="Neues Bild"
                :withIcon="true"
                icon="IconPictures"
            >
                <template #description>
                    Hier findest du eine Liste aller Bilder.
                </template>
            </navigation-card>
            <!-- Didyouknow -->
            <navigation-card v-if="canView('didyouknow')"
                class="navigation_card p-4 rounded-md bg-layout-sun-100 dark:bg-layout-night-100"
                title="DidYouKnow"
                :routeName="route('admin.tables.show','didyouknow')"
                linkName="Liste der Wussten Sie Schon Sektion"
                :routeName2="route('admin.tables.create', 'didyouknow')"
                linkName2="Neue Frage"
                :withIcon="true"
                icon="IconDidyou"
            >
                <template #description>
                                Hier findest du eine Liste aller DidYouKnow-artikel.
                </template>
            </navigation-card>
            <!-- Shortpoems Overview -->
            <navigation-card v-if="canView('shortpoems')"
                class="navigation_card p-4 rounded-md bg-layout-sun-100 dark:bg-layout-night-100"
                title="Shortpoems"
                :routeName="route('admin.tables.show','shortpoems')"
                linkName="Liste der Shortpoems"
                :routeName2="route('admin.tables.create', 'shortpoems')"
                linkName2="Neues Gedicht"
                :withIcon="true"
                icon="IconStory"
            >
                <template #description>
                                Hier findest du eine Liste aller Kurzgeschichten.
                </template>
            </navigation-card>
            <!-- Comments Overview-->
            <navigation-card v-if="canView('comments')"
                class="navigation_card p-4 rounded-md bg-layout-sun-100 dark:bg-layout-night-100"
                title="Kommentare"
                :routeName="route('admin.tables.show','comments')"
                linkName="Liste der Kommentare"
                :withIcon="true"
                icon="IconComment_bl"
            >
                <template #description>
                                Hier findest du eine Liste aller Kommentare.
                </template>
            </navigation-card>
            <!-- Tabellen Overview -->
            <navigation-card v-if="modulRights?.DataBases"
                class="navigation_card p-4 rounded-md bg-layout-sun-100 dark:bg-layout-night-100"
                title="Tabellen"
                :routeName="route('admin.tables.index', table)"
                linkName="Inhalte Verwalten"
                target="_self"
                :withIcon="true"
                icon="IconDB"
            >
                <template #description>
                    Hier kannst du alle Datenbankinhalte bearbeiten
                </template>
            </navigation-card>
            <!-- laravel.log -->
            <navigation-card v-if="modulRights?.LogViewer"
                class="navigation_card p-4 rounded-md bg-layout-sun-100 dark:bg-layout-night-100"
                title="Log"
                :routeName="route('admin.laravel_log')"
                linkName="Zum Laravel-Log"
                target="_blank"
                :withIcon="true"
                icon="IconLogs"
            >
                <!-- DB updaten -->
                <template #description> Logs anzeigen    </template>
            </navigation-card>
            <navigation-card v-if="modulRights?.UserRights"
                class="navigation_card p-4 rounded-md bg-layout-sun-100 dark:bg-layout-night-100"
                title="Rechte"
                :routeName="route('admin.users_rights')"
                linkName="Benutzerberechtigungen"
                target="_self"
                :withIcon="true"
                icon="IconRight"
            >
                <!-- DB updaten -->
                <template #description> Benutzer Rechte </template>
            </navigation-card>
        </div>
    </layout>
</template>

<script>
import { defineComponent } from "vue";

import Layout from "@/Application/Admin/Shared/Layout.vue";
import Breadcrumb from "@/Application/Components/Content/Breadcrumb.vue";

import NavigationCard from "@/Application/Components/NavigationCard.vue";
import { GetSRights,loadRights } from '@/helpers';
import { hasRight,loadAllRights,isRightsReady } from '@/utils/rights';
export default defineComponent({
    name: "Admin_Dashboard",

    components: {
        Layout,
        Breadcrumb,
        NavigationCard,
    },



  data() {
    return {
      modulRights: null,
      rightsData: {},
      rightsReady: false,
    };
  },

  async mounted() {
    this.modulRights = await loadRights();
    // console.log("Geladene Rechte:", this.modulRights);
  },
  computed: {
        routeCreate() {
            const table = CleanTable(); // oder aus props holen?
            return route('admin.tables.create', table);
        },
        isRightsReady() {
            return this.$isRightsReady;
        },
        hasRight() {
            return this.$hasRight; // Zugriff auf globale Methode
            },
    },
    methods: {
        async checkRight(right, table) {
            const value = await GetRights(right, table);
            this.rightsData[`${right}_${table}`] = value;
        },
        async hasRightLocal(right, table) {
            if (!this.rightsData[`${right}_${table}`] && table) {
                await this.checkRight(right, table);
            }
            return this.rightsData[`${right}_${table}`] === 1;
        },
        canView(table) {
        const result = this.hasRight('view', table);
        // console.log(`canView(${table}) →`, result);
        return result;
        },
    },
});

</script>
<style scoped>
/* .w-full{
    max-width: 100px !important;

} */
/* .navigation_card{
    @apply bg-primary-sun-400 dark:bg-primary-night-400;
} */

</style>
