<script>

	import axios from 'axios';
	import Dom from './components/Dom.svelte';

	const config = {
    headers: {'Access-Control-Allow-Origin': '*'}
	};

	const parser = new DOMParser();

	let size = '0';
	let doc = null;
	let responseReceived = false;
	let endpoint = 'https://dev.to/';

	axios.get(endpoint)
		.then(res => {
			doc = parser.parseFromString(res.data, 'text/html');
			size = res.headers['content-length']/1000;
			responseReceived = true;
		})
		.catch(err => {
			console.log(err);
			size = 'Fucking cors ';
		});

</script>

<style>
	.size * {
		margin: 0;
	}

	section {
		text-align: center;
	}
</style>

<svelte:head>
	<title>Size</title>
</svelte:head>

<main>
	<section>
		<div class='size'>
			<h2>~ {size}KB</h2>
			<h5>content-length</h5>
		</div>
		<h3>
			Downloaded from <a target='_blank' href={endpoint}>{endpoint}</a>
		</h3>
	</section>
	{#if responseReceived}
		<Dom doc={doc.body}/>
	{/if}
</main>