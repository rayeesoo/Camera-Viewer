# Camera-Viewer
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Front Camera Snapshot</title>
  <style>
    body {
      margin: 0;
      background: #000;
      color: #0f0;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 10px;
      height: 100vh;
      justify-content: center;
    }
    video, canvas {
      width: 100%;
      max-width: 480px;
      border: 5px solid #0f0;
      border-radius: 8px;
    }
    button {
      padding: 10px 20px;
      font-size: 1rem;
      cursor: pointer;
      border-radius: 5px;
      border: none;
      background: #0f0;
      color: #000;
    }
  </style>
</head>
<body>
  <video id="video" autoplay playsinline></video>
  <button id="capture">Capture</button>
  <!-- hidden canvas just for grabbing the frame -->
  <canvas id="canvas" style="display:none;"></canvas>

  <script>
    const video = document.getElementById('video');
    const canvas = document.getElementById('canvas');
    const btn = document.getElementById('capture');

    // 1) Start front-camera stream
    navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
      .then(stream => video.srcObject = stream)
      .catch(err => alert("Camera access failed: " + err));

    // 2) On click, draw video frame to canvas and trigger download
    btn.addEventListener('click', () => {
      const ctx = canvas.getContext('2d');
      // match canvas size to video
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      ctx.drawImage(video, 0, 0);

      // Convert canvas to blob and download
      canvas.toBlob(blob => {
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        // file name with timestamp
        a.download = `snapshot_${Date.now()}.jpg`;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
      }, 'image/jpeg', 0.95);
    });
  </script>
</body>
</html>
