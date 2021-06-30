<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
.h-6 {
  height: 1.5rem;
}
.w-6 {
  width: 1.5rem;
}
</style>

<template>
  <div>
    <div>selected result: {{ selectedOption }}</div>
    <hr>
    <VueAutosearch
      id="myId"
      v-model="selectedOption"
      :options="options"
      placeholder="direct options"
    >
      <template #:noResults>
        Es konnte kein Ergebnis gefunden werden.
      </template>
      <template #:loading>
        Lädt...
      </template>
      <template #:error>
        Es ist ein Fehler aufgetreten. Bitte versuchen Sie es erneut.
      </template>
    </VueAutosearch>
    <button @click="selectedOption = null">
      clear above
    </button>

    <br><br><br>
    <VueAutosearch
      v-model="disabledInputOption"
      :options="[]"
      placeholder="disabled input option"
      :disabled="true"
    />
    <br><br><br>

    <div>selected result: {{ selectedSearchOption }}</div>
    <hr>
    <VueAutosearch
      v-model="selectedSearchOption"
      :search-function="searchFunction"
      :max-height="400"
    />

    <br><br><br>

    With leaveBehavior = "reset"
    <div>selected result: {{ selectedSearchOptionWithReset }}</div>
    <hr>
    <VueAutosearch
      v-model="selectedSearchOptionWithReset"
      :search-function="searchFunction"
      leave-behavior="reset"
      :max-height="400"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, Ref, ref } from "vue";
import VueAutosearch from "./components/VueAutosearch.vue";

export default defineComponent({
  name: "App",
  components: {
    VueAutosearch,
  },
  setup() {
    const selectedOption = ref(null);
    const disabledInputOption = ref(null);
    const selectedSearchOption = ref(null);
    const selectedSearchOptionWithReset = ref(null);

    let searchTimeout: null | number = null;
    const searchFunction = (searchTerm: string) => {
      return new Promise((resolve) => {
        if (searchTimeout) clearTimeout(searchTimeout);

        if (searchTerm.length < 3) {
          return resolve({
            message: "needs at least 3 characters",
          });
        }

        searchTimeout = setTimeout(async () => {
          return resolve({
            result: (await (await fetch(`https://nominatim.openstreetmap.org/search.php?q=${searchTerm}&polygon_geojson=1&format=jsonv2`)).json()).map((result: { place_id: number; display_name: string }) => ({ id: result.place_id, name: result.display_name })),
          });
        }, 500);
      });
    };
    let icon_building = "<svg xmlns=\"http://www.w3.org/2000/svg\" class=\"h-6 w-6\" fill=\"none\" viewBox=\"0 0 24 24\" stroke=\"currentColor\"><path stroke-linecap=\"round\" stroke-linejoin=\"round\" stroke-width=\"2\" d=\"M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4\" /></svg>";

    const options: Ref<{ id: number; name: string; icon: string }[]> = ref([]);
    onMounted(() => {
      setTimeout(() => {
        options.value = [{ id: 1, name: "<b>first</b>", icon: icon_building }, { id: 2, name: "second", icon: "" }, { id: 3, name: "third", icon: "" }, { id: 4, name: "fourth", icon: "" }, { id: 5, name: "fifth", icon: "" }, { id: 6, name: "sixth", icon: "" }, { id: 7, name: "seventh", icon: "" }, { id: 8, name: "eight", icon: "" }, { id: 9, name: "nenth", icon: "" }, { id: 10, name: "tenth", icon: "" }];
      }, 2000);
    });

    return {
      selectedOption,
      disabledInputOption,
      selectedSearchOption,
      selectedSearchOptionWithReset,
      options,

      searchFunction,
    };
  },
});
</script>
