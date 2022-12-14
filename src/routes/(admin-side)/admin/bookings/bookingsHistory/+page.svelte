<script>
	import { onSnapshot, query, collection, orderBy, where } from 'firebase/firestore';
	import { db } from '$lib/firebase/client';
	import { onDestroy } from 'svelte';

	let listOfBooking = [];
	let sortByField = '';
	let sortByStatus = '';
	let searchByField = '';
	let searchByValue = '';
	let bookingsQuery = query(collection(db, 'booking'));

	async function getBookings(bookingsQuery) {
		const unsubscribe = onSnapshot(bookingsQuery, (querySnapshot) => {
			listOfBooking = querySnapshot.docs.map((doc) => ({ id: doc.id, ...doc.data() }));
		});
		onDestroy(() => unsubscribe());
	}

	async function changeSortBy() {
		bookingsQuery = query(collection(db, 'booking'), orderBy(sortByField, 'asc'));
	}

	async function changeSortByStatus() {
		if (sortByStatus == '') {
			bookingsQuery = query(collection(db, 'booking'), orderBy('bookDate', 'desc'));
		} else {
			bookingsQuery = query(
				collection(db, 'booking'),
				where('status', '==', sortByStatus),
				orderBy('bookDate', 'desc')
			);
		}
	}

	async function searchBookings() {
		let searchByValueCase = searchByValue.toLowerCase();
		bookingsQuery = query(
			collection(db, 'booking'),
			where(searchByField, '>=', searchByValueCase),
			where(searchByField, '<=', searchByValueCase + '~')
		);
	}

	$: getBookings(bookingsQuery);
</script>

<svelte:head>
	<title>Booking History - Southview Homes 3 Admin Panel</title>
</svelte:head>

<div class="min-w-full min-h-full bg-base-200 px-12">
	<div class="flex justify-between">
		<h1 class="text-3xl font-semibold py-12">Booking History</h1>
		<a href="/admin/bookings" class="hover:underline">Go to Bookings</a>
	</div>
	<div class="flex justify-between">
		<form on:submit|preventDefault={searchBookings}>
			<select bind:value={searchByField} required>
				<option value="" disabled selected>Search Filter</option>
				<option value="firstname">Name</option>
				<option value="email">E-mail Address</option>
				<option value="eventType">Type of Event</option>
				<option value="bookDate">Date and Time</option>
			</select>
			<input type="search" placeholder="Search here" required bind:value={searchByValue} />
		</form>
		<select bind:value={sortByField} on:change={changeSortBy}>
			<option value="" disabled selected>Sort By</option>
			<option value="firstname">Name</option>
			<option value="email">E-mail Address</option>
			<option value="eventType">Type of Event</option>
			<option value="bookDate">Date and Time</option>
		</select>
		<select bind:value={sortByStatus} on:change={changeSortByStatus}>
			<option value="" selected>Status</option>
			<option value="Approved">Approved</option>
			<option value="Disapproved">Disapproved</option>
		</select>
	</div>

	<div class="my-5 p-5 overflow-auto shadow-lg border rounded-xl bg-gray-300 hidden md:block">
		<table class="border-2 border-black bg-white w-full text-center">
			<thead class="font-bold bg-gray-500">
				<tr>
					<th class="p-3 text-sm tracking-wide">Name</th>
					<th class="p-3 text-sm tracking-wide">E-mail Address</th>
					<th class="p-3 text-sm tracking-wide">Contact No.</th>
					<th class="p-3 text-sm tracking-wide">Type of Event</th>
					<th class="p-3 text-sm tracking-wide">Date and Time</th>
					<th class="p-3 text-sm tracking-wide">Status</th>
				</tr>
			</thead>
			<tbody>
				{#each listOfBooking as book}
					{#if book.status == 'Approved' || book.status == 'Disapproved'}
						<tr class="border-t-2 border-black">
							<td class="p-3 text-sm whitespace-nowrap"
								>{book.firstNameDisplay + ' ' + book.lastNameDisplay}</td
							>
							<td class="p-3 text-sm whitespace-nowrap">{book.email}</td>
							<td class="p-3 text-sm whitespace-nowrap">{book.contactNumber}</td>
							<td class="p-3 text-sm whitespace-nowrap">{book.eventTypeDisplay}</td>
							<td class="p-3 text-sm whitespace-nowrap"
								>{book.bookDate.toDate().toLocaleDateString() +
									' at ' +
									book.bookDate.toDate().toLocaleTimeString()}</td
							>
							{#if book.status == 'Approved'}
								<td class="p-3 text-sm whitespace-nowrap text-green-500 font-bold">{book.status}</td
								>
							{:else if book.status == 'Disapproved'}
								<td class="p-3 text-sm whitespace-nowrap text-red-500 font-bold">{book.status}</td>
							{:else if book.status == 'Pending'}
								<td class="p-3 text-sm whitespace-nowrap">{book.status}</td>
							{/if}
						</tr>
					{/if}
				{/each}
			</tbody>
		</table>
	</div>

	<!-- Small screen -->
	<div class="bg-gray-300 my-5 p-5  selection:grid grid-cols-1 gap-4 md:hidden rounded-lg shadow">
		{#each listOfBooking as book}
			{#if book.status == 'Approved' || book.status == 'Disapproved'}
				<div class="bg-white space-y-3 p-4 border-2 border-black">
					<div class="flex items-center space-x-2  text-sm">
						<div>
							<span class="font-bold text-sm">Name: </span>
							{book.firstNameDisplay + ' ' + book.lastNameDisplay}
						</div>
					</div>
					<div>
						<span class="font-bold text-sm">E-mail Address: </span>
						{book.email}
					</div>
					<div>
						<span class="font-bold text-sm">Contact No: </span>
						{book.contactNumber}
					</div>
					<div>
						<span class="font-bold text-sm">Type of Event: </span>
						{book.eventTypeDisplay}
					</div>
					<div>
						<span class="font-bold text-sm">Date and Time: </span>
						{book.bookDate.toDate().toLocaleDateString() +
							' at ' +
							book.bookDate.toDate().toLocaleTimeString()}
					</div>
					<div class="font-bold">
						Status:
						{#if book.status == 'Approved'}
							<span class="text-sm text-green-500">{book.status}</span>
						{:else if book.status == 'Disapproved'}
							<span class="text-sm text-red-500">{book.status}</span>
						{/if}
					</div>
				</div>
				<br />
			{/if}
		{/each}
	</div>
</div>
