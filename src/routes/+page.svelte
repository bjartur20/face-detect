<script>
	import { onMount } from 'svelte';
	import * as faceapi from 'face-api.js';

	onMount(() => {
		const video = document.getElementById('webcam');

		const setupWebcam = () => {
			navigator.mediaDevices
				.getUserMedia({ video: true })
				.then(function (stream) {
					video.srcObject = stream;
				})
				.catch(function (error) {
					console.log('Something went wrong!');
				});
		};

		Promise.all([
			faceapi.loadSsdMobilenetv1Model('/models'),
			faceapi.loadFaceLandmarkModel('/models'),
			faceapi.loadFaceRecognitionModel('/models')
		]).then(setupWebcam);

		video.addEventListener('play', async () => {
			const canvas = faceapi.createCanvasFromMedia(video);
			document.body.append(canvas);

			const displaySize = { width: video.width, height: video.height };
			faceapi.matchDimensions(canvas, displaySize, true);

			setInterval(async () => {
				const detections = await faceapi
					.detectAllFaces(video)
					.withFaceLandmarks()
					.withFaceDescriptors();
				console.log({ detections });

				const resizedDetections = faceapi.resizeResults(detections, displaySize);

				canvas.getContext('2d')?.clearRect(0, 0, canvas.width, canvas.height);

				detections.forEach((result, i) => {
					const box = resizedDetections[i].detection.box;
					const drawBox = new faceapi.draw.DrawBox(box, {
						label: result
					});
					drawBox.draw(canvas);
				});
			}, 100);
		});
	});
</script>

<h1>Face Detect</h1>
<!-- svelte-ignore a11y-media-has-caption -->
<video id="webcam" width="600" height="450" style="width: 600px;height: 450px" autoplay></video>
