<script>

    import { createEventDispatcher, getContext, onDestroy } from 'svelte'

    export let geoJson
    export let id = 'locations'

    const ctx = getContext('map')
    const map = ctx.getMap()

    const dispatch = createEventDispatcher()

    map.addLayer({
        id,
        type: 'symbol',
        source: {
            type: 'geojson',
            data: geoJson
        },
        layout: {
            'icon-image': 'fire-station-15',
            'icon-allow-overlap': true,
            'icon-size': 1
        }
    })

    function clickHandler (e) {

        const features = map.queryRenderedFeatures(e.point, {
            layers: [id]
        })

        if (features.length) {
            dispatch('click', features[0])
        }

    }

    map.on('click', id, clickHandler)

    onDestroy(() => {
        console.log('destroy layer')
        map.off('click', id, clickHandler)
        map.removeLayer(id)
    })

</script>