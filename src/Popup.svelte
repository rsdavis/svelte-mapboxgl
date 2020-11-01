
<script>

    import { onMount } from 'svelte'

    import mapboxgl from 'mapbox-gl'
    import PopupClip from './PopupClip.svelte'
    import { getContext, onDestroy } from 'svelte'

    export let coordinates = { lat: 0, lng: 0 }
    export let maxWidth = 'none'
    export let open = true
    export let closeOnClick = true

    let ref = null
    let box = null

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

    let boxWidth = 0
    let boxHeight = 0

    onMount(() => {

        if ('ResizeObserver' in window) {

            const observer = new ResizeObserver(entries => {

                let width
                let height

                if (entries[0].borderBoxSize && entries[0].borderBoxSize.length > 0) {
                    width = entries[0].borderBoxSize[0].inlineSize
                    height = entries[0].borderBoxSize[0].blockSize
                }
                else {

                    // does not include padding
                    width = entries[0].contentRect.width
                    height = entries[0].contentRect.height

                    // includes padding
                    width = box.getBoundingClientRect().width
                    height = box.getBoundingClientRect().height

                }

                console.log(entries[0].contentRect)

                boxWidth = width
                boxHeight = height

                console.log({boxWidth, boxHeight})

            })

            observer.observe(box)

        }

    })

    onDestroy(() => {
        popup.off('open', handleOpen)
        popup.off('close', handleClose)
        popup.remove()
    })

</script>


<div bind:this={ref}>

    <PopupClip h={boxHeight} w={boxWidth} r={20}/>

    <div class='box' bind:this={box}>
        <slot />
    </div>

</div>


<style>
    .box {
        background: white;
        border-radius: 10px;
        clip-path: url(#popupClip);
        -webkit-clip-path: url(#popupClip);
        /* box-shadow: 0 1px 2px rgba(0,0,0,.1); messes up the clip-path on safari */
        padding: 1em;
    }
</style>