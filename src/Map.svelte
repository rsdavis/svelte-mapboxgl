
<script>

    import { onMount, createEventDispatcher, setContext, onDestroy } from 'svelte'
    import mapboxgl from 'mapbox-gl'

    setContext('map', { getMap: () => map })

    export let accessToken
    export let center = { lat: 0, lng: 0 }
    export let zoom = 0
    export let style = 'mapbox://styles/mapbox/light-v10'

    let map
    let container = null
    const dispatch = createEventDispatcher()

    const clickHandler = e => dispatch('click', e)

    function loadMap() {

        mapboxgl.accessToken = accessToken

        const promise = new Promise((resolve, reject) => {

            const options = { container, style, center, zoom }

            const m = new mapboxgl.Map(options)

            m.on('load', e => {
                dispatch('load', e)
                resolve(m)
            })

        })

        return promise

    }

    onMount(async () => {
        map = await loadMap()
        map.on('click', clickHandler)
    })

    onDestroy(() => {
        console.log('destroy map')
        map.off('click', clickHandler)
        map.remove()
    })

</script>

<div id='map' bind:this={container}>
    { #if map }
        <slot/>
    { /if }
</div>

<style>
    #map {
        width: 600px;
        height: 600px;
    }
</style>