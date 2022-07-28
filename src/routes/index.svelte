<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import { ethers } from 'ethers';
	import { IFrameEthereumProvider } from '../providers/iframeProvider';

	let address: string;
	let connected: boolean = false;
	let voted: boolean = false;

	let provider;

	onMount(() => {
		provider = new IFrameEthereumProvider();
		provider.connect().then(() => {
			connected = true;
		});
	});

	onDestroy(() => {
		provider?.disconnect();
	});

	const connectWeb3 = async () => {
		const res = await provider.request({ method: 'eth_requestAccounts' });
		address = res[0];
		console.log(address);
	};

	const onVote = async () => {
		const hex = ethers.utils.hexlify(
			ethers.utils.toUtf8Bytes('This would be a transaction/message signature')
		);
		const res = await provider.request({
			method: 'personal_sign',
			params: [hex, address]
		});
		voted = true;
		console.log(res);
	};
</script>

<div class="w-full h-full overflow-x-hidden overflow-y-auto">
	{#if !connected}
		Not connected to web3
	{:else if !address}
		<div>
			<button class="btn" on:click={connectWeb3}>Login To Vote</button>
		</div>
	{/if}
	<div class="overflow-x-hidden">
		<table class="table table-compact w-full text-sm">
			<!-- head -->
			<thead>
				<tr>
					<th>Proposal</th>
					<th>Current Votes</th>
				</tr>
			</thead>
			<tbody>
				<!-- row 1 -->
				<tr>
					<td>Perform a 2:1 split...</td>
					<td>123 yea - 522 nay</td>
				</tr>
				<!-- row 2 -->
				<tr>
					<td>Nominate new board member...</td>
					<td>No votes cast</td>
				</tr>
				<!-- row 3 -->
				<tr>
					<td>Update...</td>
					<td>34% yea - 67% nay</td>
				</tr>
			</tbody>
		</table>
	</div>

	{#if voted}
		<button class="btn btn-disabled">Voted</button>
	{:else if address}
		<button class="btn" on:click={onVote}>Vote</button>
	{/if}
</div>

<style>
	td,
	th {
		max-width: 60px;
		overflow: hidden;
		margin-right: 10px;
	}
</style>
