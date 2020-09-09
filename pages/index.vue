<template>
	<div class="events-wrapper">

		<div class="events-list">

			<div class="topbar">

				<h1 class="title">Events {{ eventsCount }}</h1>
				<div class="actions">

					<label>
						Order by <br>
						<select>
							<option value="default">Default</option>
							<option value="default">Category</option>
						</select>
					</label>

					<label>
						Category filter<br>
						<select v-model="categoryFilter">
							<option value="all">All</option>
							<option
								:value="cat"
								v-for="(cat, index) in allCategories"
								:key="index"
							>{{ cat }}</option>
						</select>
					</label>
				</div>

			</div>

			<div class="scrollable">
				<div class="events">
					<div
						class="event"
						:class="{
							selected : event.id == selectedEventID,
							actionexist: getColumnValue('Aksiyon', event) == 'Aksiyon Gerekmiyor' || (getColumnValue('Aksiyon', event) != '-' && getColumnValue('Aksiyon', event) != 'Aksiyon Gerekmiyor')
						}"
						v-for="event in events"
						:key="event.id"
						@click="selectRow(event.id)"
					>
						<span class="column date">
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

		<div
			class="event-details"
			v-if="selectedEvent"
		>

			<div class="topbar">

				<h1 class="title">Event Details</h1>

			</div>
			<div class="scrollable">
				<div class="details-box">

					<div
						class="actions"
						v-if="getColumnValue('Aksiyon', selectedEvent) != 'Aksiyon Gerekmiyor'"
					>
						<button @click="editAction(selectedEvent.id, 'Aksiyon Gerekmiyor')">No Action Needed</button>
						<button
							class="green"
							@click="openPopup('takeaction')"
						>Take Action</button>
					</div>

					<div class="tabs">
						<div class="tab-titles">
							<span
								:class="{active: activeTab == 'details'}"
								@click="switchTab('details')"
							>Details</span>
							<span
								:class="{active: activeTab == 'location'}"
								@click="switchTab('location')"
							>Location</span>
							<span
								:class="{active: activeTab == 'media'}"
								@click="switchTab('media')"
							>Media</span>
						</div>
						<div class="tab-contents">

							<div
								class="tab-content details"
								v-if="activeTab == 'details'"
							>

								<span
									class="detail"
									v-for="detail in selectedEvent.details"
									:key="detail.title"
								>
									<p class="title">{{ detail.title }}</p>
									<p class="value">{{ detail.title == "Tarih" ? formatDate(detail.value) : detail.value }}</p>
								</span>

								<p v-if="!selectedEvent.details.length">No details provided.</p>

							</div>

							<div
								class="tab-content location"
								v-if="activeTab == 'location'"
							>

								<div
									id="map-wrap"
									style="height: 500px"
								>
									<client-only>
										<l-map
											:zoom=18
											:center="[selectedEvent.location.latitude, selectedEvent.location.longitude]"
										>
											<l-tile-layer url="http://{s}.tile.osm.org/{z}/{x}/{y}.png"></l-tile-layer>
											<l-marker
												:lat-lng="[selectedEvent.location.latitude, selectedEvent.location.longitude]"
												:icon="customIcon"
											></l-marker>
										</l-map>
									</client-only>
								</div>

								<p v-if="selectedEvent.location == {}">There is no map information.</p>

							</div>

							<div
								class="tab-content media"
								v-if="activeTab == 'media'"
							>

								<span
									class="media"
									v-for="media in selectedEvent.media"
									:key="media.url"
								>

									<video
										:src="media.url"
										v-if="media.type == 'video'"
										controls
									></video>
									<audio
										:src="media.url"
										v-if="media.type == 'audio'"
										controls
									></audio>
									<figure v-if="media.type == 'image'">
										<img :src="media.url">
										<span
											class="image-button"
											@click="openPopup('photo')"
										>
											<img
												src="/full-size.svg"
												width="23"
												height="22"
												alt=""
											>
										</span>
									</figure>
								</span>

								<p v-if="!selectedEvent.media.length">No Media Content.</p>

							</div>

						</div>
					</div>

				</div>
			</div>

		</div>

		<div
			id="popups"
			v-if="activePopup"
		>
			<div
				class="popup take-action"
				v-if="activePopup == 'takeaction'"
			>

				<span
					class="close-button"
					@click="closePopups()"
				>
					<img
						src="/times.svg"
						alt=""
					>
				</span>

				<div class="tabs">
					<div class="tab-titles">
						<span
							:class="{active: activeActionTab == 'selectaction'}"
							@click="switchActionTab('selectaction')"
						><span class="number">1</span> Select Action</span>
						<span :class="{active: activeActionTab == 'takeaction'}"><span class="number">2</span> Take Action</span>
					</div>
					<div class="tab-contents">

						<div
							class="tab-content selectaction"
							v-if="activeActionTab == 'selectaction'"
						>

							<span
								class="choice"
								:class="{selected : selectedAction == 'Resolved'}"
								@click="switchAction('Resolved')"
							>
								<p class="title">Mark as Resolved</p>
								<p class="desctription">Mark this event as resolved and enter the details of the resolution.</p>
							</span>

							<span
								class="choice"
								:class="{selected : selectedAction == 'Change Asset'}"
								@click="switchAction('Change Asset')"
							>
								<p class="title">Change Asset</p>
								<p class="desctription">Change the asset with another one.</p>
							</span>

							<div class="nav text-center">
								<button
									class="green"
									@click="switchActionTab('takeaction')"
									:disabled="selectedAction == null"
								>Next</button>
							</div>

						</div>

						<div
							class="tab-content takeaction"
							v-if="activeActionTab == 'takeaction'"
						>

							<div
								class="choice-info"
								v-if="selectedAction == 'mark-resolved'"
							>
								<p class="title">Mark as Resolved</p>
								<p class="desctription">Mark this event as resolved and enter the details of the resolution.</p>
							</div>

							<div
								class="choice-info"
								v-if="selectedAction == 'change-asset'"
							>
								<p class="title">Change Asset</p>
								<p class="desctription">Change the asset with another one.</p>
							</div>

							<div class="resolution-detail">
								<label for="resolution-detail">Resolution Detail*</label>
								<textarea
									id="resolution-detail"
									placeholder="Enter resolution detail…"
									v-model="resolutionDetail"
									:maxlength="maxChar"
								></textarea>
								<div class="char-count">({{ currentChar }}/{{ maxChar }})</div>
							</div>

							<div class="nav text-center">
								<button @click="switchActionTab('selectaction')">Back</button>
								<button
									class="green"
									@click="openPopup('loading')"
								>Take Action</button>
							</div>

						</div>

					</div>
				</div>

			</div>

			<div
				class="popup loading"
				v-if="activePopup == 'loading'"
			>

				<span
					class="close-button"
					@click="closePopups()"
				>
					<img
						src="/times.svg"
						alt=""
					>
				</span>

				<div class="popup-content">

					<img
						src="/loading.svg"
						alt="Loading"
						class="spin"
					>

				</div>

			</div>

			<div
				class="popup action-result success"
				v-if="activePopup == 'action-taken'"
			>

				<span
					class="close-button"
					@click="closePopups()"
				>
					<img
						src="/times.svg"
						alt=""
					>
				</span>

				<div class="popup-content text-center">

					<img
						src="/check.svg"
						alt=""
					>
					<div class="title">ACTION HAS BEEN TAKEN!</div>
					<p class="description">You can see the action details from details tab.</p>

				</div>

			</div>

			<div
				class="popup action-result error"
				v-if="activePopup == 'action-error'"
			>

				<span
					class="close-button"
					@click="closePopups()"
				>
					<img
						src="/times.svg"
						alt=""
					>
				</span>

				<div class="popup-content text-center">

					<img
						src="/cross.svg"
						alt=""
					>
					<div class="title">A PROBLEM OCCURED!</div>
					<p class="description">We cannot continue due to a problem. <br> Please try again later.</p>

				</div>

			</div>

			<div
				class="popup photo"
				v-if="activePopup == 'photo'"
			>

				<figure>

					<img
						:src="selectedEvent.media[0].url"
						alt=""
					>
					<span
						class="image-button"
						@click="closePopups()"
					>
						<img
							src="/exit-full-screen.svg"
							alt=""
						>
					</span>

				</figure>

			</div>

		</div>

	</div>
</template>

<script>
	import { example_response } from "~/static/example_response_js_file.txt";

	let L = { icon() { } };
	if (process.browser) L = require('leaflet');

	export default {
		data() {
			return {
				getEvents: example_response.data,
				selectedID: null,

				activeTab: "details",
				activePopup: null,
				activeActionTab: "selectaction",
				selectedAction: null,
				resolutionDetail: "",

				sortBy: "default",
				categoryFilter: "all",

				maxChar: 300,
				customIcon: L.icon({
					iconUrl: '/marker.svg',
					iconSize: [40, 40],
					iconAnchor: [21, 35],
					popupAnchor: [-3, -76]
				})
			}
		},
		computed: {
			events() {

				let events = this.getEvents;

				// Category Filter
				if (this.categoryFilter != "all") {

					// events = events.filter(event => {

					// 	let details = event.details;
					// 	let catCol = details.find(col => col.title == "Kategori");
					// 	console.log(catCol);

					// 	return catCol.value == this.categoryFilter;
					// });

					events = events.filter(event => {

						return event.details.find(col => col.title == "Kategori").value == this.categoryFilter;

					});

				}

				return events;
			},
			eventsCount() {
				return this.events.length;
			},
			allCategories() {
				let categories = [];

				this.getEvents.forEach(event => {

					categories.push(
						event.details.find(col => col.title == "Kategori").value
					);

				});

				let uniqueCats = [...new Set(categories)];
				return uniqueCats;
			},
			selectedEventID() {

				let ID = this.selectedID;

				if (this.events.length) ID = this.events[0].id;
				if (this.selectedID != null && this.events.find(event => event.id == this.selectedID)) ID = this.selectedID;
				if (!this.events.length) ID = null;

				return ID;
			},
			selectedEvent() {
				return this.events.find(event => event.id == this.selectedEventID);
			},
			currentChar() {
				return this.resolutionDetail.length;
			}
		},
		methods: {
			formatDate(dateString) {
				let date = new Date(dateString)

				// Add zero to minutes
				const minutes = date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes();

				return date.getDate() + "." + (date.getMonth() + 1) + "." + date.getFullYear() + "  " + date.getHours() + ":" + minutes;
			},
			getColumnValue(colName, event) {
				return event.details.find(col => col.title == colName).value;
			},
			selectRow(eventID) {
				this.selectedID = eventID;
			},
			switchTab(tabName) {
				this.activeTab = tabName;
			},
			switchActionTab(tabName) {
				this.activeActionTab = tabName;
			},
			switchAction(action) {
				this.selectedAction = action;
			},
			editAction(eventID, newValue) {

				//console.log('EDIT ACTION: ', eventID, newValue);

				// Find the event
				let theEvent = this.events.find(event => event.id == eventID);
				if (!theEvent) return false;
				let theEventIndex = this.events.findIndex(event => event.id == eventID);
				//console.log('Event Found: ', theEvent, theEventIndex);

				// Find the action column
				let actionColumn = theEvent.details.find(col => col.title == "Aksiyon");
				if (!actionColumn) return false;
				let actionColumnIndex = theEvent.details.findIndex(col => col.title == "Aksiyon");
				//console.log('Action Column: ', actionColumn, actionColumnIndex);

				// Modify
				this.events[theEventIndex].details[actionColumnIndex].value = newValue;

			},
			openPopup(popupName) {
				this.activePopup = popupName;

				if (popupName == 'loading') {

					setTimeout(() => {

						this.openPopup(this.currentChar > 0 ? 'action-taken' : 'action-error');

						if (this.currentChar > 0) {
							this.editAction(this.selectedEventID, this.selectedAction);
						}

					}, 1000);

				}
			},
			closePopups() {
				this.activePopup = null;
				this.selectedAction = null;
				this.activeActionTab = "selectaction";
				this.resolutionDetail = "";
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

			.topbar {
				display: flex;
				justify-content: space-between;
				align-items: center;
				padding-top: 20px;
				padding-bottom: 10px;

				.actions {
					display: flex;
					gap: 20px;
					font-size: 12px;
					padding-right: 10px;
				}
			}
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
					border-right: 5px solid white;
					padding: 5px 10px 0;
					box-shadow: 0px 3px 6px #00000014;
					min-height: 70px;
					display: grid;
					grid-template-columns: 120px repeat(4, 1fr);
					gap: 30px;
					justify-content: center;
					align-items: center;
					cursor: pointer;

					&:hover {
						opacity: 0.7;
					}

					&.actionexist {
						border-left-color: $white;
					}

					&.selected {
						opacity: 1;
						background-color: #fbf5d7; // 0.2 opacity of $yellow - transparentize($yellow, 0.8) not working because the background is not white
						border-left-color: $yellow;
						border-right-color: #e7e2c6;
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
					margin-bottom: 25px;
				}

				.tabs {
					.details {
						display: grid;
						grid-template-columns: 1fr 1fr;
						gap: 20px;

						& > .detail {
							.title {
								font-weight: 700;
							}
						}
					}
				}
			}
		}

		#popups {
			position: fixed;
			z-index: 1000;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			display: flex;
			justify-content: center;
			align-items: center;
			background-color: transparentize($dark-blue, 0.3);

			& > .popup {
				width: 50vw;
				min-height: 330px;
				max-height: 95vh;
				overflow: auto;
				background-color: $white;
				border-radius: 12px;
				padding: 40px;
				position: relative;
				display: flex;
				justify-content: center;
				align-items: center;

				.close-button {
					position: absolute;
					right: 20px;
					top: 15px;
					cursor: pointer;

					&:hover {
						opacity: 0.7;
					}
				}

				.tab-titles {
					justify-content: center;
					gap: 60px;

					.number {
						color: $white;
						background-color: $dark-blue;
						width: 19px;
						height: 19px;
						border-radius: 50%;
						display: inline-flex;
						justify-content: center;
						align-items: center;
						font-size: 12px;
					}
				}

				.choice {
					display: block;
					margin-top: 12px;
					padding: 12px 18px;
					background-color: $gray;
					box-shadow: 0px 3px 6px #00000029;
					font-size: 16px;
					user-select: none;
					cursor: pointer;

					.title {
						font-weight: bold;
					}

					&:hover {
						opacity: 0.8;
					}

					&.selected {
						background-color: #454f63;
						color: white;
						opacity: 1;
					}
				}

				.choice-info {
					font-size: 16px;

					.title {
						font-weight: bold;
					}
				}

				.resolution-detail {
					margin-top: 20px;
					position: relative;

					label {
						display: block;
						font-weight: bold;
						margin-bottom: 3px;
					}

					textarea {
						resize: none;
					}

					.char-count {
						position: absolute;
						right: 10px;
						bottom: 10px;
					}
				}

				&.photo {
					border-radius: 0;
					width: auto;
					min-width: auto;
					max-width: 100vw;
					height: auto;
					min-height: auto;
					padding: 10px;

					img {
						display: block;
						max-width: auto;
					}
				}

				&.action-result {
					.title {
						font-size: 29px;
						font-weight: 700;
						margin-top: 10px;
						margin-bottom: 10px;
					}

					.description {
						font-size: 16px;
					}

					&.success .title {
						color: $green;
					}

					&.error .title {
						color: $red;
					}
				}

				.nav {
					margin-top: 30px;
					margin-bottom: -10px;
				}
			}
		}
	}
</style>
