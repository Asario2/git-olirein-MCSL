<template>
    <layout
        header-title="Anwender ist kein Mitarbeiter"
        :header-url="$page.props.saas_url + '/home/user_is_no_employee'"
    >
        <page-content>
            <template #content>
                <page-title>
                    <template #title>Anwender ist kein Mitarbeiter</template>
                </page-title>
                <page-paragraph>
                    <template #paragraph>
                        Die gewünschte Seite ist nicht zugänglich, da du nicht
                        über Mitarbeiterrechte verfügst!
                    </template>
                </page-paragraph>
            </template>
        </page-content>
    </layout>
</template>
<script>
import { defineComponent } from "vue";
import Layout from "@/Application/Homepage/Shared/Layout.vue";

import PageContent from "@/Application/Components/Content/PageContent.vue";
import PageTitle from "@/Application/Components/Content/PageTitle.vue";
import PageParagraph from "@/Application/Components/Content/PageParagraph.vue";

export default defineComponent({
    name: "Homepage_UserIsNoEmployee",

    components: {
        Layout,
        PageContent,
        PageTitle,
        PageParagraph,
    },
});
</script>
