<script>
	import { userStore } from '$lib/store';
	import { getDoc, doc, addDoc, collection } from 'firebase/firestore';
	import { db } from '$lib/firebase/client';
	import { goto } from '$app/navigation';

	let user = null;
	const dateMin = new Date(Date.now() + 8.64e+7).toLocaleDateString('en-ca');
	const dateMax = new Date((Date.now() + 8.64e+7) + (6.048e+8 * 2)).toLocaleDateString('en-ca');


	let guest = {
		firstname: '',
		lastname: '',
		email: '',
		contactNumber: '',
		status: 'Pending',
		paymentStatus: 'Unpaid',
		eventType: '',
		date: '',
		time: ''
	};

	async function getUser() {
		const snapshot = await getDoc(doc(db, 'accounts', $userStore.uid));
		user = snapshot.data();
		guest.firstname = user.firstNameDisplay;
		guest.lastname = user.lastNameDisplay;
	}
	$: if ($userStore) {
		getUser();
	}

	async function submitHandler() {
		if (user !== null) {
			try {
				await addDoc(collection(db, 'booking'), {
					firstname: guest.firstname.trim().toLowerCase(),
					firstNameDisplay: guest.firstname,
					lastname: guest.lastname.trim().toLowerCase(),
					lastNameDisplay: guest.lastname,
					email: user.email.trim().toLowerCase(),
					contactNumber: user.contactNumber,
					status: guest.status,
					paymentStatus: guest.paymentStatus,
					eventType: guest.eventType.trim().toLowerCase(),
					eventTypeDisplay: guest.eventType,
					bookDate: new Date(guest.date + ' ' + guest.time)
				});
				alert('Reservation form submitted');
				await goto('/');
			} catch (error) {
				console.log(error);
				alert('Error sending request');
			}
		} else {
			try {
				await addDoc(collection(db, 'booking'), {
					firstname: guest.firstname.trim().toLowerCase(),
					firstNameDisplay: guest.firstname,
					lastname: guest.lastname.trim().toLowerCase(),
					lastNameDisplay: guest.lastname,
					email: guest.email.trim().toLowerCase(),
					contactNumber: guest.contactNumber,
					status: guest.status,
					paymentStatus: guest.paymentStatus,
					eventType: guest.eventType.trim().toLowerCase(),
					eventTypeDisplay: guest.eventType,
					bookDate: new Date(guest.date + ' ' + guest.time)
				});
				alert('Schedule request submitted');
				await goto('/calendar');
			} catch (error) {
				console.log(error);
				alert('Error sending request');
			}
		}
	}
</script>

<svelte:head>
	<title>Clubhouse Reservation - Official Website of Southview Homes 3 Subdivision</title>
</svelte:head>

<div class="min-h-screen hero bg-base-200">
	<div class="w-full max-w-4xl p-6 mx-auto shadow-2xl border rounded-xl bg-base-100">
		<h1 class="text-2xl mt-2">Clubhouse Reservation Form</h1>
		<form on:submit|preventDefault={submitHandler}>
			<div class="grid grid-cols-1 gap-6 mt-6 md:grid-cols-2">
				{#if user}
					<div class="form-control">
						<span class="label-text">First Name</span>
						<input
							type="text"
							class="border-2 rounded-lg p-3 mt-2"
							bind:value={guest.firstname}
							required
						/>
					</div>
					<div class="form-control">
						<span class="label-text">Last Name</span>
						<input
							type="text"
							class="border-2 rounded-lg p-3 mt-2"
							bind:value={guest.lastname}
							required
						/>
					</div>
					<div class="form-control ">
						<span class="label-text">E-mail Address</span>
						<input
							type="text"
							class="border-2 rounded-lg p-3 mt-2"
							bind:value={user.email}
							required
						/>
					</div>
					<div class="form-control">
						<span class="label-text">Contact No.</span>
						<input
							type="tel"
							onkeypress="return event.charCode >= 48 && event.charCode <= 57"
							minlength="11" maxlength="11"
							placeholder="09123456789"
							pattern={String.raw`^(09)\d{9}$`}
							class="border-2 rounded-lg p-3 mt-2"
							bind:value={user.contactNumber}
							required
						/>
					</div>
				{:else}
					<div class="form-control">
						<span class="label-text">First Name</span>
						<input
							type="text"
							bind:value={guest.firstname}
							class="border-2 rounded-lg p-3 mt-2"
							required
						/>
					</div>
					<div class="form-control">
						<span class="label-text">Last Name</span>
						<input
							type="text"
							bind:value={guest.lastname}
							class="border-2 rounded-lg p-3 mt-2"
							required
						/>
					</div>
					<div class="form-control">
						<span class="label-text">E-mail Address</span>
						<input
							type="email"
							bind:value={guest.email}
							name="email"
							class="border-2 rounded-lg p-3 mt-2"
							required
						/>
					</div>
					<div class="form-control">
						<span class="label-text">Contact No.</span>
						<input
							type="tel"
							onkeypress="return event.charCode >= 48 && event.charCode <= 57"
							minlength="11" maxlength="11"
							placeholder="09123456789"
							pattern={String.raw`^(09)\d{9}$`}
							bind:value={guest.contactNumber}
							name="contact"
							class="border-2 rounded-lg p-3 mt-2"
							required
						/>
					</div>
				{/if}
				<div class="form-control">
					<span class="label-text">Type of Event</span>
					<input
						type="text"
						class="border-2 rounded-lg p-3 mt-2"
						bind:value={guest.eventType}
						required
					/>
				</div>
				<div class="form-control">
					<span class="label-text">Date</span>
					<input
						type="date"
						min={dateMin}
						max={dateMax}
						class="border-2 rounded-lg p-3 mt-2"
						bind:value={guest.date}
						required
					/>
				</div>
				<div class="form-control">
					<span class="label-text">Time</span>
					<input
						type="time"
						min="8:00"
						max="19:00"
						class="border-2 rounded-lg p-3 mt-2"
						bind:value={guest.time}
						required
					/>
				</div>
			</div>
			<div class="flex justify-end mt-8">
				<button type="submit" class="btn btn-primary">Submit Schedule</button>
				<a href="/calendar" class="btn btn-error mx-1">Cancel</a>
			</div>
		</form>
	</div>
</div>
