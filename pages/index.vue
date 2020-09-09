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
					<div
						class="event"
						:class="{ selected : index == 1 }"
						v-for="(event, index) in events"
						:key="event.id"
					>
						<span class="column date">
							<!-- {{ event.details[0].value }} -->
							<p class="title">Date</p>
							<p class="value">{{ formatDate( getColumnValue('Tarih', event) ) }}</p>
						</span>
						<span class="column type">
							<p class="title">Type</p>
							<p class="value">{{ getColumnValue('Tip', event) }}</p>
						</span>
						<span class="column bin-id">
							<p class="title">Bin ID</p>
							<p class="value"><a href="#">{{ event.id }}</a></p>
						</span>
						<span class="column temprature">
							<p class="title">Category</p>
							<p class="value">{{ getColumnValue('Kategori', event) }}</p>
						</span>
						<span class="column action">
							<p class="title">Action</p>
							<p class="value">{{ getColumnValue('Aksiyon', event) }}</p>
						</span>
					</div>
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
	import { example_response } from "~/static/example_response_js_file.txt";

	export default {
		data() {
			return {
				events: example_response.data
			}
		},
		methods: {
			formatDate(dateString) {
				let date = new Date(dateString)

				// Add zero to minutes
				const minutes = date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes();

				return date.getDate() + "." + (date.getMonth() + 1) + "." + date.getFullYear() + "  " + date.getHours() + ":" + minutes;
			},
			getColumnValue(colName, data) {
				return data.details.find(col => col.title == colName).value;
			}
		},
	}
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
			padding-bottom: 10px;
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
					border-left: 9px solid $yellow;
					padding: 5px 10px 0;
					box-shadow: 0px 3px 6px #00000014;
					min-height: 70px;
					display: grid;
					grid-template-columns: 120px 1fr 90px 1fr 1fr;
					gap: 30px;
					justify-content: center;
					align-items: center;
					cursor: pointer;

					&:hover {
						opacity: 0.7;
					}

					&.selected {
						opacity: 1;
						background-color: #fbf5d7; // 0.2 opacity of $yellow - transparentize($yellow, 0.8) not working because the background is not white
					}

					& > .column {
						& > * {
							margin: 0;
							font-size: 13px;
						}

						& > .title {
							font-weight: 700;
						}
					}
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
