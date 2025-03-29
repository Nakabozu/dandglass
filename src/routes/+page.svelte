<script>
		/**
	 * This app is a simple countdown timer from 60 seconds to 0.
	 * It shows a picture of Daniel, who, when clicked, will start the countdown.
	 * At first, it zooms in on his head slowly, but as the time gets closer to 0,
	 * the background around Daniels head will fade to a picture of space.
	 * Daniel's head will start spinning rapidly as a ticking timer grows increasingly louder.
	 * In the final ten seconds, pictures of Daniel will briefly flash on the screen behind
	 * Daniel's spinning head and the timer until it reaches 0; at which time
	 * an explosion will remove Daniel's head and the timer from the screen.
	 * Finally, a picture of Daniel will appear in the center of the screen with a caption
	 * "MINUTE TO WIN IT" and a button to restart the countdown.
	 */

	import { onMount, tick } from 'svelte';
	import explosionSound from '$lib/explosion.mp3';
	import tickSound from '$lib/beep.mp3';
	import tockSound from '$lib/boop.mp3';
	import explosionGif from '$lib/images/explosion.gif';
	import spaceImage from '$lib/images/space.jpg';
	import danHeadImage from '$lib/images/DanHead.png';
	import backSvg from '$lib/images/back.svg';
	import muteSvg from '$lib/images/mute.svg';
	import unmuteSvg from '$lib/images/unmute.svg';

	const danielImages = [
		'DanCake.jpg',
		'DanChill.jpg',
		'DanCork.jpg',
		'DanDapper.jpg',
		'DanDines.jpg',
		'DanDrink.jpg',
		'DanDrool.jpg',
		'DanDrunk.jpg',
		'DanHead.png',
		'DanJapan.jpg',
		'DanMouse.jpg',
		'DanPeace.jpg',
		'DanPumpkin.jpg',
		'DanPutt.jpg',
		'DanRelax.jpg',
		'DanRink.jpg',
		'DanScreen.jpg',
		'DanSmile.jpg',
		'DanSnuggle.jpg',
		'DanSquat.jpg',
		'DanSteve.jpg',
		'DanTrain.jpg',
		'DanTreat.jpg',
		'DanWine.jpg'
	];
	// const preloadImageUrls = ['DanHead.png', 'explosion.gif', 'space.jpg'].map((img) => `$lib/images/${img}`);
	let explosionSoundElement, tickSoundElement, tockSoundElement;

	let countdownMinutes = $state(1);
	let countdownSeconds = $state(0);
	let countdownMilliseconds = $state(0);
	let isCountingDown = $state(false);
	let isDone = $state(false);
	let isNextTickATock = $state(false);
	let danSpinDuration = $state(10);

	let clear;
	let timeToStopAt = $state(Date.now());
	let isMuted = $state(false);

	const startCountdown = () => {
		isCountingDown = true;
		timeToStopAt = addTimeToNow(countdownMinutes, countdownSeconds);
		clear = setInterval(() => {
			const { minutes, seconds, milliseconds } = timeDifference(Date.now(), timeToStopAt);
			if (minutes <= 0 && seconds <= 0 && milliseconds <= 0) {
				isDone = true;
				countdownMinutes = 0;
				countdownSeconds = 0;
				countdownMilliseconds = 0;
				clearInterval(clear);
				if (!isMuted) {
					explosionSoundElement.play().catch((error) => {
						console.error('Error playing explosion sound:', error);
					});
				}
				return;
			}
			if (!isMuted) {
				if (minutes <= 0 && seconds <= 30 && seconds < countdownSeconds) {
					if (seconds < 10) {
						danSpinDuration = seconds + 1;
					}

					tickSoundElement.volume = (30 - seconds) / 30; // Set volume for tick sound
					tockSoundElement.volume = (30 - seconds) / 30; // Set volume for tock sound

					if (isNextTickATock) {
						tockSoundElement.play().catch((error) => {
							console.error('Error playing tock sound:', error);
						});
					} else {
						tickSoundElement.play().catch((error) => {
							console.error('Error playing tick sound:', error);
						});
					}
					isNextTickATock = !isNextTickATock; // Toggle between tick and tock
				}
			}
			// Update the countdown state
			countdownMinutes = minutes;
			countdownSeconds = seconds;
			countdownMilliseconds = milliseconds;
		}, 25);
	};

	const stopCountdown = () => {
		clearInterval(clear);
		isCountingDown = false;
		countdownMinutes = 1;
		countdownSeconds = 0;
		countdownMilliseconds = 0;
		isDone = false;
		danSpinDuration = 10; // Reset spin duration
		isNextTickATock = false; // Reset tick/tock state
		timeToStopAt = Date.now();
	};

	/**
	 * Adds specified time units to the current date and time
	 * @param {number} minutes - Number of minutes to add (can be negative)
	 * @param {number} seconds - Number of seconds to add (can be negative)
	 * @param {number} milliseconds - Number of milliseconds to add (can be negative)
	 * @returns {Date} A new Date object with the added time
	 */
	function addTimeToNow(minutes = 0, seconds = 0, milliseconds = 0) {
		// Create a new Date object representing the current date and time
		const now = new Date();

		// Convert all time units to milliseconds and add them
		const minutesInMs = minutes * 60 * 1000;
		const secondsInMs = seconds * 1000;
		const totalMilliseconds = minutesInMs + secondsInMs + milliseconds;

		// Add the total milliseconds to the current time
		now.setTime(now.getTime() + totalMilliseconds);

		return now;
	}

	/**
	 * Gets the time between the current time and the given time as a string of MM:SS.mm
	 */
	function timeDifference(time1, time2) {
		const date1 = new Date(time1);
		const date2 = new Date(time2);

		const diffInMs = date2 - date1;
		const diffInSeconds = Math.floor(diffInMs / 1000);
		const minutes = Math.floor(diffInSeconds / 60);
		const seconds = diffInSeconds % 60;
		const milliseconds = Math.floor(diffInMs % 1000);

		return { minutes, seconds, milliseconds };
	}
</script>

<svelte:head>
	<title>DANIEL'S MINUTE TO WIN IT</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
	{#if isCountingDown}
		<audio src={explosionSound} bind:this={explosionSoundElement}></audio>
		<audio src={tickSound} bind:this={tickSoundElement}></audio>
		<audio src={tockSound} bind:this={tockSoundElement}></audio>
		<button class="back-button" onclick={stopCountdown} aria-label="Go back">
			<img height="50px" src={backSvg} alt="Back icon" />
		</button>
		{#if isMuted}
			<button
				class="mute-button"
				onclick={() => {
					isMuted = false;
				}}
				aria-label="Mute"
			>
				<img height="50px" src={muteSvg} alt="Mute Icon" />
			</button>
		{:else}
			<button
				class="unmute-button"
				onclick={() => {
					isMuted = true;
				}}
				aria-label="Go back"
			>
				<img height="50px" src={unmuteSvg} alt="Unmute Icon" />
			</button>
		{/if}
		<div class="background">
			<img src={spaceImage} alt="Space Background" class="background-image" />
		</div>
		{#if isDone}
			<img src={explosionGif} alt="Explosion" class="explosion" />
		{/if}

		<div class="timer">
			<h1>
				{String(countdownMinutes).padStart(2, '0')}
				:{String(countdownSeconds).padStart(2, '0')}
				.{String(countdownMilliseconds).padStart(3, '0')}
			</h1>
			{#if isDone}
				<button onclick={stopCountdown}>Again?</button>
			{/if}
		</div>
		{#if !isDone}
			<img
				src={danHeadImage}
				alt="Dan Head"
				class="dan-head spin"
				style="--spin-duration: {danSpinDuration}s;"
			/>
		{/if}
	{:else}
		<div style="gap: 8px; display: flex; flex-direction: column; align-items: center;">
			<span style="display: flex; flex-direction: row; align-items: center; gap: 4px;">
				<input
					type="number"
					inputmode="numeric"
					pattern="[0-9]+"
					max="60"
					min="0"
					bind:value={countdownMinutes}
				/>
				minutes
				<input
					type="number"
					inputmode="numeric"
					pattern="[0-9]+"
					max="59"
					min="0"
					bind:value={countdownSeconds}
				/>
				seconds
			</span>
			<button class="start-button" onclick={startCountdown}> START </button>
		</div>
	{/if}
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}

	img {
		pointer-events: none;
	}

	.background-image {
		z-index: 1;
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
	}

	.dan-head {
		position: fixed;
		width: 25rem; /* Adjust the size as needed */
		z-index: 2;
	}

	input {
		font-family: 'DSEG7';
		height: fit-content;
		width: 3rem;
		text-align: right;
		padding: 4px 8px;
	}

	.back-button,
	.mute-button,
	.unmute-button {
		position: fixed;
		top: 0;
		z-index: 5;
		background-color: transparent;
		border: none;
		cursor: pointer;
	}

	.back-button {
		left: 0;
	}

	.mute-button,
	.unmute-button {
		right: 0;
	}

	.start-button {
		width: 100%;
	}

	.explosion {
		z-index: 5;
		position: fixed;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		width: 125dvw; /* Adjust the size as needed */
		animation-name: goaway;
		animation-delay: 1.4s;
		animation-duration: 0.01s;
		animation-timing-function: linear;
		animation-fill-mode: forwards;
		animation-iteration-count: 1;
	}

	.timer h1 {
		font-family: 'DSEG7';
		position: fixed;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		font-size: 10dvw;
		line-height: 100dvw;
		margin: 0px;
		padding: 0px;
		text-wrap: nowrap;
		z-index: 3;
		color: red;
	}

	.spin {
		/* Animation properties */
		animation-name: spin;
		animation-duration: var(--spin-duration, 3s); /* Default 3 seconds if not specified */
		animation-timing-function: linear;
		animation-iteration-count: infinite;

		/* Ensure the element's transform origin is centered */
		transform-origin: center;

		/* Optional: preserve 3D for smoother animation */
		transform-style: preserve-3d;
		backface-visibility: hidden;

		/* If the element is inline, make it a block to avoid weird behavior */
		display: inline-block;
	}

	@keyframes goaway {
		from {
			visibility: visible;
		}
		to {
			visibility: hidden;
		}
	}

	@keyframes spin {
		from {
			transform: rotate(0deg);
		}
		to {
			transform: rotate(360deg);
		}
	}
</style>
