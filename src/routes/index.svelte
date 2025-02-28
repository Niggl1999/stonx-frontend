<script lang="ts">
	import { ApiClient } from '$lib/ApiClient';
	import { IconService } from '$lib/IconService';
	import Statscard from '$lib/Statscard.svelte';
	import Chart from '$lib/Chart.svelte';
	import DiversityChart from '$lib/DiversityChart.svelte';
	import UserStore from '$lib/UserStore';
	import * as cryptoIcons from 'base64-cryptocurrency-icons';

	$: bitpanda_indices = [];
	$: bitpanda_wallets = [];
	$: binance_wallets = [];
	$: current_wallets = [];
	$: current_tokens = [];
	$: diversity = [];
	$: portfolio_timeseries = {
		day: [],
		week: [],
		month: [],
		year: [],
		all: []
	};

	let promises: Promise<any>[] = new Array<Promise<any>>();

	if (UserStore.state.exchanges.includes('BITPANDA')) {
		promises.push(
			ApiClient.getBitpandaIndices().then((res) => {
				bitpanda_indices = res;
			})
		);
		promises.push(
			ApiClient.getBitpandaCrypto().then((res) => {
				bitpanda_wallets = res;
			})
		);
	}
	if (UserStore.state.exchanges.includes('BINANCE')) {
		promises.push(
			ApiClient.getBinanceSpot().then((res) => {
				binance_wallets = res;
			})
		);
	}
	promises.push(
		ApiClient.getWallets().then((res) => {
			current_wallets = res;
			current_tokens = current_wallets?.map((w) => w.token).filter((v, i, a) => a.indexOf(v) === i);
		})
	);

	ApiClient.getTotalTimeSeries('TODAY').then((res) => {
		portfolio_timeseries.day = res;
	});
	ApiClient.getTotalTimeSeries('THISWEEK').then((res) => {
		portfolio_timeseries.week = res;
	});
	ApiClient.getTotalTimeSeries('THISMONTH').then((res) => {
		portfolio_timeseries.month = res;
	});
	ApiClient.getTotalTimeSeries('THISYEAR').then((res) => {
		portfolio_timeseries.year = res;
	});
	ApiClient.getTotalTimeSeries('ALL').then((res) => {
		portfolio_timeseries.all = res;
	});
	ApiClient.getPortfolioDiversity().then((res) => {
		diversity = res;
	});
</script>

<div>
	<div class="w-full px-4 sm:px-6 md:px-8">
		<h1 class="text-2xl font-semibold text-gray-900">Dashboard</h1>
	</div>
	<div class="w-full py-5 px-4 sm:px-6 lg:px-8">
		<dl
			class="grid grid-cols-1 gap-5 lg:grid-cols-2 xl:grid-cols-3 2xl:grid-cols-4 3xl-grid-cols-5"
		>
			<div class="col-span-1 lg:col-span-2 xl:col-span-2 2xl:col-span-3 h-6/6">
				<Chart bind:values={portfolio_timeseries} />
			</div>
			<div class="col-span-1 lg:col-span-2 xl:col-span-1 2xl:col-span-1 h-5/6">
				<DiversityChart bind:values={diversity} />
			</div>
			{#await Promise.all(promises)}
				<p>Loading data....</p>
			{:then}
				<Statscard
					title="Total"
					value={(
						bitpanda_wallets.reduce((sum, cur) => (sum = sum + cur.fiat), 0) +
						bitpanda_indices.reduce((sum, cur) => (sum = sum + cur.fiat), 0) +
						binance_wallets.reduce((sum, cur) => (sum = sum + cur.fiat), 0) +
						current_wallets.reduce((sum, cur) => (sum = sum + cur.fiat), 0)
					).toFixed(2) + ' €'}
				>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						class="text-white"
						width="48"
						height="48"
						viewBox="0 0 24 24"
						><path fill="none" d="M0 0h24v24H0z" /><path
							fill="currentColor"
							d="M12 22C6.477 22 2 17.523 2 12S6.477 2 12 2s10 4.477 10 10-4.477 10-10 10zm0-2a8 8 0 100-16 8 8 0 000 16zm-1.95-9H15v2h-4.95a2.5 2.5 0 004.064 1.41l1.7 1.133A4.5 4.5 0 018.028 13H7v-2h1.027a4.5 4.5 0 017.788-2.543L14.114 9.59A2.5 2.5 0 0010.05 11z"
						/></svg
					>
				</Statscard>
				{#if bitpanda_wallets.length > 0}
					<Statscard
						title="Bitpanda Tokens"
						link="/bitpanda"
						link_text="Bitpanda assets"
						value={bitpanda_wallets.reduce((sum, cur) => (sum = sum + cur.fiat), 0).toFixed(2) +
							' €'}
						><svg
							xmlns="http://www.w3.org/2000/svg"
							class="text-white"
							width="48"
							height="48"
							viewBox="0 0 24 24"
							><path fill="none" d="M0 0h24v24H0z" /><path
								fill="currentColor"
								d="M12 22C6.477 22 2 17.523 2 12S6.477 2 12 2s10 4.477 10 10-4.477 10-10 10zm0-2a8 8 0 100-16 8 8 0 000 16zm-1-4H8V8h3V6h2v2h1a2.5 2.5 0 012 4 2.5 2.5 0 01-2 4h-1v2h-2v-2zm-1-3v1h4a.5.5 0 100-1h-4zm0-3v1h4a.5.5 0 100-1h-4z"
							/></svg
						>
					</Statscard>
				{/if}
				{#if bitpanda_indices.length > 0}
					<Statscard
						title="Bitpanda Index"
						link="/bitpanda"
						link_text="Bitpanda assets"
						value={bitpanda_indices.reduce((sum, cur) => (sum = sum + cur.fiat), 0).toFixed(2) +
							' €'}
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="text-white"
							width="48"
							height="48"
							viewBox="0 0 24 24"
							><path fill="none" d="M0 0h24v24H0z" /><path
								fill="currentColor"
								d="M4 5v14h16V5H4zM3 3h18a1 1 0 011 1v16a1 1 0 01-1 1H3a1 1 0 01-1-1V4a1 1 0 011-1zm11.793 6.793L13 8h5v5l-1.793-1.793-3.864 3.864-2.121-2.121-2.829 2.828-1.414-1.414 4.243-4.243 2.121 2.122 2.45-2.45z"
							/></svg
						></Statscard
					>
				{/if}
				{#if binance_wallets.length > 0}
					<Statscard
						title="Binance Spot Wallets"
						link="/binance"
						link_text="Binance Spot Wallets"
						value={binance_wallets.reduce((sum, cur) => (sum = sum + cur.fiat), 0).toFixed(2) +
							' €'}
						><svg
							xmlns="http://www.w3.org/2000/svg"
							class="text-white"
							width="48"
							height="48"
							viewBox="0 0 24 24"
							><path fill="none" d="M0 0h24v24H0z" /><path
								fill="currentColor"
								d="M12 22C6.477 22 2 17.523 2 12S6.477 2 12 2s10 4.477 10 10-4.477 10-10 10zm0-2a8 8 0 100-16 8 8 0 000 16zm-1-4H8V8h3V6h2v2h1a2.5 2.5 0 012 4 2.5 2.5 0 01-2 4h-1v2h-2v-2zm-1-3v1h4a.5.5 0 100-1h-4zm0-3v1h4a.5.5 0 100-1h-4z"
							/></svg
						>
					</Statscard>
				{/if}
				{#each current_tokens as token}
					<Statscard
						title={`${token.toUpperCase()} Wallets`}
						link="/wallets"
						link_text="Wallets"
						value={current_wallets
							.filter((w) => w.token == token)
							.reduce((sum, cur) => (sum = sum + cur.fiat), 0)
							.toFixed(2) + ' €'}
					>
						<img height="48" width="48" alt={token} src={IconService.getTokenIconBase64(token)} />
					</Statscard>
				{/each}
			{/await}
		</dl>
	</div>
</div>
