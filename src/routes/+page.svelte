<script>
	import { browser } from '$app/environment';

	if (browser) {
		const video = document.getElementById('webcam');
		if (navigator.mediaDevices.getUserMedia) {
			navigator.mediaDevices
				.getUserMedia({ video: true })
				.then(function (stream) {
					video.srcObject = stream;
				})
				.catch(function (error) {
					console.log('Something went wrong!');
				});
		}
	}
</script>

<h1>Welcome to SvelteKit</h1>
<video id="webcam" autoplay></video>
