<script>
	let {
		imageSrc = '/src/lib/images/DanHead.png',
		startingHeight = 748,
		startingY = 748,
		startingX = 1111,
		finalY = 748,
		finalX = 1111,
		className = 'zoom-image-container'
	} = $props();
</script>

<div>
	<!-- The hidden image will auto-size the div's width for us to keep it at the right aspect ratio without any fancy calculations -->
	<img src={imageSrc} alt="Fennel Claws" class="hidden-image" />
	<img
		style="top: -{startingY}px; 
            left: -{startingX}px; 
            height: {startingHeight}%; 
            --finalY: {finalY}px; 
            --finalX: {finalX}px; 
            --finalZoom: {startingHeight}%"
		class="zoom-image"
		src={imageSrc}
		alt="Fennel Claws"
	/>
</div>

<style>
	div {
		position: relative;

		height: 500px;
		overflow: hidden;

		align-content: center;

		z-index: 2;
	}

	.hidden-image {
		opacity: 0.01;
		height: 100%;
		max-width: 100%;
	}

	.zoom-image {
		position: absolute;

		animation-name: zoomOut;
		animation-delay: 5s;
		animation-duration: 15s;
		/* Smooth animation */
		animation-timing-function: linear;
		/* Keep the final styles of the animation when the animation completes */
		animation-fill-mode: forwards;
	}

	@keyframes zoomOut {
		to {
			height: var(--finalZoom);
			top: var(--finalY);
			left: var(--finalX);
		}
	}
</style>
