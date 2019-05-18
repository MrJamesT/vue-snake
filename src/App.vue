<template>
	<v-app id="myapp">
		<v-container align-center justify-center grid-list-md text-xs-center fill-height>
			<v-layout align-center justify-center column>
				<alert-dialog :modalWindow="modalWindow" :message="message" @close-modal="modalWindow = false"/>
				<game-header :gameRunning="gameRunning" @modify-interval="modifyInterval($event)"/>
				<game-field
					:gameRunning="gameRunning"
					@game-finished="gameRunning = false"
					@alert-message="sendAlert($event)"
				/>
			</v-layout>
		</v-container>
	</v-app>
</template>

<script>
import GameHeader from './components/GameHeader'
import GameField from './components/GameField'
import AlertDialog from './components/AlertDialog'

export default {
	name: 'App',
	components: {
		GameHeader,
		GameField,
		AlertDialog
	},
	data() {
		return {
			modalWindow: false,
			gameRunning: false,
			message: {}
		}
	},
	methods: {
		sendAlert(evt) {
			this.message = evt
			this.modalWindow = true
		},
		modifyInterval(evt) {
			if (evt == 'start') this.gameRunning = true
			else this.gameRunning = false
		}
	}
}
</script>

<style lang="scss" scoped>
#myapp {
	overflow: hidden;
	background: #3a1c71;
	background: -webkit-linear-gradient(to right, #ffaf7b, #d76d77, #3a1c71);
	background: linear-gradient(to right, #ffaf7b, #d76d77, #3a1c71);
}
</style>

