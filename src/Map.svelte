<svelte:options tag="element-factory-google-map" />

<script>
  import { onMount } from "svelte";
  export let width = "100%";
  export let height = "400px";
  export let zoom = 10;
  export let center = "25.2048,55.2708";
  // width="100%" height="300px" center="25.2048,55.2708" zoom="10"
  //
  export let points = [];
  export let markers = [];
  export let search = false;

  window.initMapComponent = initMapComponent;

  let element;
  let map;
  let mapReady = false;
  let _markers = [];

  function parseJSONArrayProp(val, propName) {
    if (!val) return [];
    if (val.constructor === String) {
      try {
        return JSON.parse(val);
      } catch (err) {
        console.error(`Failed parsing json array property ${propName}:`, err);
        return [];
      }
    } else if (val.constructor === Array) {
      return val;
    } else {
      return [];
    }
  }

  /* 
  defaults applied if some values missing and has defaults 
  */
  function applyDefaults() {
    const defaultProps = {
      center: center || "25.2048,55.2708",
      zoom: zoom || 10,
      markers: [
        {
          position: { lat: 25.2048, lng: 55.2708 },
          title: "Marker 1 Title",
          label: "1",
          draggable: false,
        },
        {
          title: "Marker 2 Title",
          label: "2",
          position: { lat: 25.25, lng: 55.29 },
          draggable: false,
        },
      ],
    };
    return defaultProps;
  }

  function transformProps() {
    return {
      points: parseJSONArrayProp(points, "points"),
      markers: parseJSONArrayProp(markers, "markers"),
      zoom: +zoom,
      center: new google.maps.LatLng(
        ...center.split(",").map((el) => el?.trim())
      ),
    };
  }

  function initMapComponent(e) {
    // console.log('initMapComponent')
    ({ zoom, center, markers } = applyDefaults());
    ({ center, zoom } = transformProps());
    console.log("maps.Icon", google.maps);

    map = new google.maps.Map(element.querySelector("#map"), {
      zoom,
      center,
    });

    const infoWindow = new google.maps.InfoWindow();
    _markers = markers.map((el) => {
      const _marker = new google.maps.Marker({
        ...el,
        map: map,
      });

      _marker.addListener("click", () => {
        console.log("click");
        infoWindow.close();
        infoWindow.setContent(_marker.getTitle());
        infoWindow.open(_marker.getMap(), _marker);
      });
    });

    mapReady = true;
  }
  onMount(() => {
    console.log("document", document.getElementById("map"));
  });
</script>

<svelte:head>
  <!-- <script
    defer
    async
    src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&callback=initMapComponent"></script>
  </script> -->
</svelte:head>
<main bind:this={element}>
  <div class="globalCss" style="width:{width};height:{height}">
    <div id="map" class="itemCss" style="width: {width};height:{height}" />
    <script
      id
      src="https://maps.googleapis.com/maps/api/js?key=AIzaSyB41DRUbKWJHPxaFjMAwdrzWzbVKartNGg&v=weekly&callback=initMapComponent"
      defer></script>
    {#if mapReady}
      <google-map
        {map}
        fit-to-markers
        api-key="AIzaSyD3E1D9b-Z7ekrT3tbhl_dy8DCXuIuDDRc"
        search
      />
    {/if}
  </div>
</main>

<style lang="scss">
  #map {
    height: 400px;
    width: 100%;
  }
</style>
