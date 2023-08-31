<script setup lang="ts">
const address = ref("");
const selectedKeyword = ref("restaurant");
const places = ref<any>([]);

const keywordList = ["restaurant", "cafe", "bar", "pub"];

const requestStatus = ref("idle");

function search() {
  requestStatus.value = "loading";
  // @ts-ignore
  const geocoder = new google.maps.Geocoder();
  geocoder.geocode(
    { address: address.value },
    async (results: any, status: any) => {
      // @ts-ignore
      if (status === google.maps.GeocoderStatus.OK) {
        const userLocation = results[0].geometry.location;
        // console.log(userLocation);

        // @ts-ignore
        const { PlacesService } = await google.maps.importLibrary("places");
        // console.log(PlacesService);

        // @ts-ignore
        const placesService = new PlacesService(document.createElement("div"));

        placesService.nearbySearch(
          {
            location: userLocation,
            radius: 1000,
            keyword: selectedKeyword.value,
          },
          (placesResults: any, placesStatus: any) => {
            // @ts-ignore
            if (placesStatus === google.maps.places.PlacesServiceStatus.OK) {
              // Process and display the list of potential candidates for places
              // 'placesResults' contains the list of places matching the criteria
              console.log(placesResults);
              places.value = placesResults;
              requestStatus.value = "success";
            } else {
              requestStatus.value = "error";
              console.error("Error fetching nearby places:", placesStatus);
            }
          }
        );
      } else {
        requestStatus.value = "error";
        console.error("Error geocoding address:", status);
      }
    }
  );
}

function getImageUrl(place: any) {
  return place.photos?.[0].getUrl() ?? "";
}
</script>

<template>
  <div class="min-h-screen flex justify-center items-start mt-20">
    <div class="">
      <div class="flex justify-center items-center gap-5">
        <input
          type="text"
          v-model="address"
          placeholder="Enter address"
          class="border border-gray-300 rounded-md px-4 py-2 w-96"
        />
        <div>
          <select
            id="keyword"
            name="keyword"
            v-model="selectedKeyword"
            class="block w-full rounded-md py-3 px-2 text-gray-900 ring-1 ring-inset ring-gray-300 sm:text-sm sm:leading-6"
          >
            <option
              v-for="keyword in keywordList"
              :key="keyword"
              :value="keyword"
            >
              {{ keyword }}
            </option>
          </select>
        </div>

        <button
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
          @click="search"
        >
          Search
        </button>
      </div>

      <div class="mt-10 max-w-2xl max-h-[80vh] overflow-y-auto">
        <!-- loading -->
        <div v-if="requestStatus === 'loading'">
          <p>Loading...</p>
        </div>
        <div v-else-if="requestStatus === 'error'">
          <p>Error fetching nearby places</p>
        </div>
        <ul v-else role="list" class="divide-y divide-gray-100">
          <li
            v-for="place in places"
            :key="place.place_id"
            class="flex gap-x-4 py-5"
          >
            <img
              class="h-12 w-12 flex-none rounded-full bg-gray-50"
              :src="getImageUrl(place)"
              alt=""
            />
            <div class="min-w-0">
              <p class="text-sm font-semibold leading-6 text-gray-900">
                {{ place.name }}
              </p>
              <p class="mt-1 truncate text-xs leading-5 text-gray-500">
                {{ place.vicinity }}
              </p>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
