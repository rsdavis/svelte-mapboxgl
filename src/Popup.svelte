
<script>

    import mapboxgl from 'mapbox-gl'

    import { getContext, onDestroy } from 'svelte'

    export let coordinates = { lat: 0, lng: 0 }
    export let maxWidth = 'none'
    export let open = true
    export let closeOnClick = true

    let ref = null

    const map = getContext('map').getMap()

    function handleOpen () {
        open = true
    }

    function handleClose () {
        open = false
    }

    const popup = new mapboxgl.Popup({ closeOnClick })
        .setMaxWidth(maxWidth)
        .on('open', handleOpen)
        .on('close', handleClose)
        .addTo(map)

    $: ref          && popup.setDOMContent(ref)
    $: maxWidth     && popup.setMaxWidth(maxWidth)
    $: coordinates  && popup.setLngLat(coordinates)

    $: open ? popup.addTo(map) : popup.remove()

    onDestroy(() => {
        popup.off('open', handleOpen)
        popup.off('close', handleClose)
        popup.remove()
    })

</script>

<span bind:this={ref}>
    <slot />
</span>