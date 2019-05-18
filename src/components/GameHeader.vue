<template>
	<div class="mb-5">
		<h3 class="display-2 white--text mb-3">Simple Snake!</h3>
		<h5 class="headline white--text mb-3">Challenge yourself with the hardest game on the planet</h5>
		<v-btn v-if="gameRunning" color="error" @click="$emit('modify-interval', 'stop')">Stop</v-btn>
		<v-btn v-else color="primary" @click="$emit('modify-interval', 'start')">Start</v-btn>
	</div>
</template>

<script>
export default {
	props: {
		gameRunning: {
			type: Boolean,
			default: false
		}
	}
}
</script>

<style lang="scss" scoped>
</style>