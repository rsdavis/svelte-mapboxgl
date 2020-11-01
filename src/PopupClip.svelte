<script>
	
	export let w = 400
	export let h = 300
    export let r = 50
    export let id = 'popupClip'
	
	$: path = `M 0 0 h ${w-r} a ${r} ${r} 0 0 0 ${r} ${r} v ${h-r} h ${-w} Z`
	
</script>

<svg>
  <defs>
    <clipPath id={id}>
      <path d={path}></path>
    </clipPath>
  </defs>
</svg>

<style>

    svg {
        display: block;
        height: 0;
        width: 0;
    }

	/* .clip-svg {
		clip-path: url(#myClip);
	} */
</style>
