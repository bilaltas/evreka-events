<template>
	<div class="events-wrapper">
		<div class="events-list">

			<div class="topbar">

				<h1 class="title">Events</h1>
				<div
					class="actions"
					style="display: none"
				>Sort & Filter</div>

			</div>
			<div class="scrollable">
				<div class="events">
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
					<div class="event">ASD</div>
				</div>
			</div>

		</div>
		<div class="event-details">

			<div class="topbar">

				<h1 class="title">Event Details</h1>

			</div>
			<div class="scrollable">
				<div class="details-box">
					<div class="actions">
						<button>No Action Needed</button>
						<button class="green">Take Action</button>
					</div>
				</div>
			</div>

		</div>
	</div>
</template>

<script>
	export default {}
</script>

<style lang="scss">
	.events-wrapper {
		display: grid;
		grid-template-columns: 855px 1fr;
		gap: 10px;
		height: inherit;

		& > * {
			display: grid;
			grid-template-columns: 1fr;
			grid-template-rows: 75px minmax(0, 1fr);
			height: inherit;

			& > .scrollable {
				overflow: auto;
			}
		}

		.topbar {
			display: flex;
			justify-content: space-between;
			align-items: center;
			padding-top: 20px;
		}

		.events-list {
			.title {
				margin-left: 18px;
			}

			.events {
				display: grid;
				grid-template-columns: 1fr;
				gap: 10px;

				.event {
					background-color: $white;
					box-shadow: 0px 3px 6px #00000014;
				}
			}
		}

		.event-details {
			.details-box {
				background-color: $white;
				padding: 20px;

				.actions {
					display: grid;
					grid-template-columns: 1fr 1fr;
					gap: 7px;
				}
			}
		}
	}
</style>
