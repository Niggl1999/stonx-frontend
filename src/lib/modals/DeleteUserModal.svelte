<script lang="ts">
	import { ApiClient } from '$lib/ApiClient';
	import UserStore from '$lib/UserStore';

	export let delete_user_modal_open = true;
	$: processed_last_submit = true;

	(() => {
		document.onkeydown = (e) => {
			e = e || (window.event as KeyboardEvent);
			if (e.key === 'Escape') {
				close();
			}
			if (e.keyCode === 13) {
				submit();
			}
		};
	})();

	function close() {
		delete_user_modal_open = false;
	}

	function submit() {
		if (!processed_last_submit) {
			return;
		}
		processed_last_submit = false;
		ApiClient.deleteMe(true).then((result) => {
			close();
			UserStore.logout();
		});
	}
</script>

<div
	class:overflow-hidden={delete_user_modal_open}
	hidden={!delete_user_modal_open}
	class="fixed z-10 inset-0 overflow-y-auto"
>
	<div
		class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
	>
		<transition
			enter-active-class="ease-out duration-300"
			enter-class="opacity-0"
			enter-to-class="opacity-100"
			leave-active-class="ease-in duration-200"
			leave-class="opacity-100"
			leave-to-class="opacity-0"
		>
			<div class="fixed inset-0 transition-opacity" aria-hidden="true">
				<div class="absolute inset-0 bg-gray-500 opacity-75" />
			</div>
		</transition>

		<!-- This element is to trick the browser into centering the modal contents. -->
		<span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">​</span>
		<transition
			enter-active-class="ease-out duration-300"
			enter-class="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
			enter-to-class="opacity-100 translate-y-0 sm:scale-100"
			leave-active-class="ease-in duration-200"
			leave-class="opacity-100 translate-y-0 sm:scale-100"
			leave-to-class="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
		>
			<div
				class="inline-block align-bottom bg-white rounded-lg px-4 pt-5 pb-4 text-left shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full sm:p-6"
				role="dialog"
				aria-modal="true"
				aria-labelledby="modal-headline"
			>
				<div class="hidden sm:block absolute top-0 right-0 pt-4 pr-4">
					<button
						on:click={close}
						type="button"
						class="bg-white rounded-md text-gray-400 hover:text-gray-500 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
					>
						<svg
							class="h-6 w-6"
							xmlns="http://www.w3.org/2000/svg"
							fill="none"
							viewBox="0 0 24 24"
							stroke="currentColor"
							aria-hidden="true"
						>
							<path fill="none" d="M0 0h24v24H0z" /><path
								d="M12 10.586l4.95-4.95 1.414 1.414-4.95 4.95 4.95 4.95-1.414 1.414-4.95-4.95-4.95 4.95-1.414-1.414 4.95-4.95-4.95-4.95L7.05 5.636z"
							/>
						</svg>
					</button>
				</div>
				<div class="sm:items-start">
					<div class="mt-3 text-center sm:mt-0 sm:ml-4 sm:text-left">
						<h3 class="text-lg leading-6 font-medium text-gray-900" id="modal-headline">
							Profile deletion
						</h3>
						<div class="mt-2">
							<p class="text-sm text-gray-500">
								Deletes your profile along all of the wallets and exchanges you added from this
								service! <br />
								This doesn't delete any other profiles or wallets.<br />
								We advise that you revoke any api credentials you used here after deleting your profile
								(we don't keep it either).
							</p>
						</div>
						<div class="mt-5 sm:mt-4">
							<button
								on:click={close}
								type="button"
								class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white text-base font-medium text-gray-700 hover:text-gray-500 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:mt-0 sm:w-auto sm:text-sm"
							>
								Cancel
							</button>
							<button
								on:click={submit}
								type="button"
								class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-red-600 text-base font-medium text-white hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500 sm:ml-3 sm:w-auto sm:text-sm"
							>
								Confirm, delete my profile
							</button>
						</div>
					</div>
				</div>
			</div></transition
		>
	</div>
</div>
