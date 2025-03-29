<script>
	import '../app.css';

	let { children } = $props();
</script>

<div class="app">
	<main>
		{@render children()}
	</main>
</div>

<style>
	    @font-face{
        font-family: "DSEG7";
        src: url('../lib/DSEG7Classic-Regular.ttf') format('truetype');
    }

	.app {
		display: flex;
		flex-direction: column;
		min-height: 100vh;
	}

	main {
		flex: 1;
		display: flex;
		flex-direction: column;
		height: 100dvh;
		width: 100%;
		margin: 0 auto;
		box-sizing: border-box;
		background-image: url($lib/images/DanCollage.png);
		background-size: cover;
		background-position: center;
		background-repeat: no-repeat;
	}
</style>
