<script>
	import { db } from '$lib/firebase/client';
	import { query, collection, getCountFromServer, where } from 'firebase/firestore';

	let countOfPendingBooks = '';
	let countOfAccounts = '';

	async function getCount() {
		try {
			// Get count of pending books
			const collectionOfPendingBooksQuery = query(
				collection(db, 'booking'),
				where('status', '==', 'Pending')
			);
			const snapshotOfCountOfPendingBookings = await getCountFromServer(
				collectionOfPendingBooksQuery
			);
			countOfPendingBooks = snapshotOfCountOfPendingBookings.data().count;
			// Get count of number of accounts
			const collectionOfAccountsQuery = collection(db, 'accounts');
			const snapshotOfCountOfAccounts = await getCountFromServer(collectionOfAccountsQuery);
			countOfAccounts = snapshotOfCountOfAccounts.data().count;
		} catch (error) {
			console.log(error);
			alert('Error in counting');
		}
	}

	getCount();
</script>

<svelte:head>
	<title>Southview Homes 3 Admin Panel</title>
</svelte:head>

<div class="min-w-full min-h-full bg-base-200">
	<h1 class="text-3xl font-semibold p-12">Dashboard</h1>
	<div class="flex flex-col md:flex-row pt-4">
		<div class="basis-1/3 flex flex-col items-center lg:items-end space-y-4 p-5">
			<div
				class="w-60 h-40 flex flex-col justify-center items-center overflow-hidden shadow-lg border rounded-xl bg-base-100"
			>
				<p class="text-3xl font-semibold">43</p>
				<p class="text-md mt-2">Pending Dues</p>
			</div>
			<div
				class="w-60 h-40 flex flex-col justify-center items-center overflow-hidden shadow-lg border rounded-xl bg-base-100"
			>
				<p class="text-3xl font-semibold">{countOfPendingBooks}</p>
				<p class="text-md mt-2">Booking Requests</p>
			</div>
			<div
				class="w-60 h-40 flex flex-col justify-center items-center overflow-hidden shadow-lg border rounded-xl bg-base-100"
			>
				<p class="text-3xl font-semibold">{countOfAccounts}</p>
				<p class="text-md mt-2">Accounts</p>
			</div>
		</div>
		<div class="basis-2/3 flex justify-center p-5">
			<div class="w-full max-w-3xl shadow-2xl border rounded-xl bg-base-100">
				<div class="p-4">
					<h1 class="text-2xl mb-8 font-semibold p-3">Upcoming Events</h1>
					<div class="flex flex-col space-y-8 text-md p-3">
						<div>
							<p class="font-medium text-md mb-3">Christmas Party</p>
							<p>Dec 18 3:00 PM</p>
						</div>
						<div>
							<p class="font-medium text-md mb-3">New Years Gathering</p>
							<p>Dec 31 10:00 PM</p>
						</div>
						<div>
							<p class="font-medium text-md mb-3">Meeting</p>
							<p>Jan 3 8:00 PM</p>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</div>
