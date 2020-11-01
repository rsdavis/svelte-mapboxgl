<script>
    import Map from "./Map.svelte";
    import Popup from "./Popup.svelte";
    import Layer from "./Layer.svelte";

    import spots from "./_spots.js";

    const coordinates = { lng: -88, lat: 30 };
    let count = 0

    function createGeoJson(spots) {

        const features = spots.map((spot) => ({
            type: "Feature",
            geometry: {
                type: "Point",
                coordinates: [spot.fields.lng, spot.fields.lat],
            },
            properties: {
                id: spot.id,
                name: spot.fields.name,
                hasImg: spot.fields.images,
                img: spot.fields.images
                    ? spot.fields.images[0].thumbnails.large.url
                    : null,
            },
        }));

        const geoJson = {
            type: "FeatureCollection",
            features: features,
        };

        return geoJson;
    }

    $: geoJson = createGeoJson(spots)


    let popupOpen = true
    let popupCoords = { lng: -88, lat: 30 }

    function handleLayerClick (event) {

        const feature = event.detail

        popupCoords = feature.geometry.coordinates
        popupOpen = true

    }

</script>

<div class='map-container'>

    <Map
        accessToken="pk.eyJ1IjoicnNkYXZpcyIsImEiOiJja2ZvZTY4aW4wOTVyMnluNXl1MGt2MW5wIn0.JeYnGnOYfkXkwILRNAQi3g"
        center={coordinates}
        zoom="7"
        on:click={() => popupOpen = false}
    >

        <Popup coordinates={popupCoords} bind:open={popupOpen} closeOnClick={false}>
            <img src="https://picsum.photos/200/200" alt="">
        </Popup>

        <Layer { geoJson } on:click={handleLayerClick}/>

    </Map>

</div>

<style>

    .map-container {
        height: 100vh;
    }

    h2 {
        border: 1px solid black;
        margin: 0;
    }
    img {
        display: block;
    }
</style>
