<script lang="ts">
	import { Tween } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';
	const resetDuration = 300;
	let flipped = $state(false);
	let dragged = $state(false);
	let offsetY = $state(0);
	let tweenedOffsetX = new Tween(0, {
		easing: cubicOut,
		duration: 0,
	});
	let tweenedOffsetY = new Tween(0, {
		easing: cubicOut,
		duration: 0,
	});

	function onMouseDown() {
		// Only drag if card is flipped.
		if (!flipped) {
			return
		}
		// Handle cases when the card is still moving back to its original position.
		if (tweenedOffsetX.current != tweenedOffsetX.target) {
			tweenedOffsetX.target = tweenedOffsetX.current;
		}
		if (tweenedOffsetY.current != tweenedOffsetY.target) {
			tweenedOffsetY.target = tweenedOffsetY.current;
		}
		dragged = true;
	}

	function onMouseUp() {
		dragged = false;
		tweenedOffsetX.set(0, { duration: resetDuration });
		tweenedOffsetY.set(0, { duration: resetDuration });
	}

	function onMouseMove(ev: MouseEvent) {
		if (!dragged) {
			return;
		}
		offsetY += ev.movementY;
		tweenedOffsetX.target += ev.movementX;
		tweenedOffsetY.target += ev.movementY;
	}

</script>

<div class="container">
	<button
		class="dragger"
		onmousedown={onMouseDown}
		onmousemove={onMouseMove}
		onmouseup={onMouseUp}
		onclick={() => flipped = flipped || !flipped}
		style:transform="translate({tweenedOffsetX.current}px, {tweenedOffsetY.current}px)"
	>
		<div
			class={["card", {flipped}, {dragged}]}
		>
			<div class="front bg-base-content">
				<span class="symbol">♠</span>
			</div>
			<div class="back bg-base-200">
				<div class="pattern"></div>
			</div>
		</div>
	</button>
</div>

<style>
	.container {
		display: flex;
		flex-direction: column;
		height: 100%;
		align-items: center;
		justify-content: center;
	}

	.card {
		position: relative;
		aspect-ratio: 2.5 / 3.5;
		font-size: min(1vh, 0.25rem);
		height: 120em;
		border-radius: 2em;
		transform: rotateY(180deg);
		transition: transform 0.4s;
		transform-style: preserve-3d;
		padding: 0;
		user-select: none;
		cursor: pointer;
	}

	.card.flipped {
		transform: rotateY(0);
		background-color: #555
	}

	.front, .back {
		display: flex;
		align-items: center;
		justify-content: center;
		position: absolute;
		width: 100%;
		height: 100%;
		left: 0;
		top: 0;
		backface-visibility: hidden;
		border-radius: 2em;
		border: 1px solid #fff;
		box-sizing: border-box;
		padding: 2em;
	}

	.back {
		transform: rotateY(180deg);
	}

	.symbol {
		font-size: 30em;
		color: var(--fg-1);
	}

	.pattern {
		width: 100%;
		height: 100%;
		background-color: var(--bg-2);
		/* pattern from https://projects.verou.me/css3patterns/#marrakesh */
		background-image:
		radial-gradient(var(--bg-3) 0.9em, transparent 1em),
		repeating-radial-gradient(var(--bg-3) 0, var(--bg-3) 0.4em, transparent 0.5em, transparent 2em, var(--bg-3) 2.1em, var(--bg-3) 2.5em, transparent 2.6em, transparent 5em);
		background-size: 3em 3em, 9em 9em;
		background-position: 0 0;
		border-radius: 1em;
	}
</style>

