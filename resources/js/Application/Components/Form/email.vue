<template>
<h1 class="2xl">Kontakt</h1>
<form @submit.prevent="submitForm"
class="max-w-xl mx-auto space-y-6 p-6
        bg-white dark:bg-layout-night-50
        rounded-xl shadow-md
        border border-layout-sun-1050 dark:border-layout-night-1050 lg:rounded-lg">

<div>
    <label class="block font-semibold mb-1 text-layout-sun-1000 dark:text-layout-night-1000">Name</label>
    <input v-model="form.name" type="text"
        class="input border border-layout-sun-1050 dark:border-layout-night-1050" required />
</div>

<div>
    <label class="block font-semibold mb-1 text-layout-sun-1000 dark:text-layout-night-1000">E-Mail</label>
    <input v-model="form.email" type="email"
        class="input border border-layout-sun-1050 dark:border-layout-night-1050" required />
</div>

<div>
    <label class="block font-semibold mb-1 text-layout-sun-1000 dark:text-layout-night-1000">Betreff</label>
    <input v-model="form.subject" type="text"
        class="input border border-layout-sun-1050 dark:border-layout-night-1050" required />
</div>

<div>
    <label class="block font-semibold mb-1 text-layout-sun-1000 dark:text-layout-night-1000">Nachricht</label>
    <textarea v-model="form.message" rows="5"
            class="input border border-layout-sun-1050 dark:border-layout-night-1050" required></textarea>
</div>

<div>
    <label class="block font-semibold mb-1 text-layout-sun-1000 dark:text-layout-night-1000">
        Sicherheitsfrage: Was ist 3 + 4?
    </label>
    <input v-model="form.captcha" type="text"
        class="input border border-layout-sun-1050 dark:border-layout-night-1050" required />
</div>

<div class="flex items-center">
    <input v-model="form.accepted" type="checkbox" id="accept" class="mr-2" />
    <label for="accept" class="text-layout-sun-1000 dark:text-layout-night-1000">
        Ich akzeptiere die Datenschutzbestimmungen
    </label>
</div>

<div>
    <button type="submit"
            class="btn"
            :disabled="!form.accepted">
        Absenden
    </button>
</div>
</form>



</template>
<script>
import { defineComponent } from "vue";
import { selectionHelper, GetSettings,rumLaut } from "@/helpers";


export default defineComponent({
    name: "emailview",

    components: {

    },
    props:{
        news:[Array,Object],
        text: [Array,Object],
    },
    data() {
    return {
      form: {
        name: '',
        email: '',
        subject: '',
        message: '',
        captcha: '',
        accepted: false
      }
    }
  },

    methods: {
        cleanHtml(html) {
      const result = rumLaut(html);
     // console.log("rumLaut output:", result);
      return result;

    },
    async submitForm() {
      try {
        const response = await axios.post('/contact/send', this.form)
        alert('Nachricht erfolgreich gesendet!')
        this.resetForm()
      } catch (error) {
        alert('Fehler beim Senden der Nachricht.')
      }
    },
    resetForm() {
      this.form = {
        name: '',
        email: '',
        subject: '',
        message: '',
        captcha: '',
        accepted: false
      }
    }
  },
});
</script>
<style scoped>
.input {
  @apply w-full px-4 py-2 border rounded-md dark:bg-zinc-800 dark:text-white;
}

.btn {
  @apply px-6 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 disabled:opacity-50;
}
</style>
