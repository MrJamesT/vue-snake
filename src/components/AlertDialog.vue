<template>
	<v-dialog v-model="modalWindow" persistent max-width="290" v-if="message">
		<v-card>
			<v-card-title class="headline">{{ message.heading }}</v-card-title>
			<v-card-text>{{ message.text }}</v-card-text>
			<v-card-actions>
				<v-spacer></v-spacer>
				<v-btn color="red darken-1" flat @click="$emit('close-modal')">Close</v-btn>
				<v-btn color="green darken-1" flat @click="$emit('close-modal')">Upload Score</v-btn>
			</v-card-actions>
		</v-card>
	</v-dialog>
</template>

<script>
export default {
	props: ['modalWindow', 'message']
}
</script>

<style lang="scss" scoped>
</style>