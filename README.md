<!DOCTYPE html>
<html>
<head>
  <title>Gesture Detection</title>
</head>
<body>
  <h1>Hand Gesture Detection</h1>
  <video id="webcam" autoplay playsinline width="300" height="200"></video>
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest"></script>
  <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@latest"></script>
  <script>
    const URL = "https://teachablemachine.withgoogle.com/models/8HrLrkeiA/";
    let model, webcam, prediction;

    async function init() {
      const modelURL = URL + "model.json";
      const metadataURL = URL + "metadata.json";

      model = await tmImage.load(modelURL, metadataURL);
      webcam = new tmImage.Webcam(300, 200, true);
      await webcam.setup();
      await webcam.play();
      window.requestAnimationFrame(loop);

      document.body.appendChild(webcam.canvas);
    }

    async function loop() {
      webcam.update();
      prediction = await model.predict(webcam.canvas);
      if (prediction[0].probability > 0.95) {
        // 손 흔들기 감지됨 → CoSpaces로 신호 보내기
        window.parent.postMessage("wave_detected", "*");
      }
      window.requestAnimationFrame(loop);
    }

    init();
  </script>
</body>
</html>
