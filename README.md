<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ROM Player</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            padding: 20px;
        }
        #emulator {
            border: 1px solid black;
            margin-top: 20px;
        }
        #dropzone {
            border: 2px dashed #ccc;
            padding: 20px;
            margin-top: 20px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>Drag and Drop ROM Player</h1>
    <div id="dropzone">
        Drag & Drop ROM Here or Click to Browse
        <input type="file" id="fileInput" style="display:none;" accept=".nes">
    </div>
    <canvas id="emulator" width="256" height="240"></canvas>

    <script src="https://cdn.jsdelivr.net/npm/jsnes@1.1.0/dist/jsnes.min.js"></script>
    <script>
        const nes = new jsnes.NES({
            onFrame: function(frameBuffer) {
                const canvas = document.getElementById('emulator');
                const context = canvas.getContext('2d');
                const imageData = context.createImageData(256, 240);
                for (let i = 0; i < frameBuffer.length; i++) {
                    imageData.data[i * 4 + 0] = frameBuffer[i] & 0xFF;
                    imageData.data[i * 4 + 1] = (frameBuffer[i] >> 8) & 0xFF;
                    imageData.data[i * 4 + 2] = (frameBuffer[i] >> 16) & 0xFF;
                    imageData.data[i * 4 + 3] = 0xFF;
                }
                context.putImageData(imageData, 0, 0);
            },
            onStatusUpdate: function(status) {
                console.log(status);
            },
            onAudioSample: function(left, right) {
                // Handle audio samples if needed
            }
        });

        function playROM(file) {
            const reader = new FileReader();
            reader.onload = function() {
                nes.loadROM(reader.result);
                nes.start();
            };
            reader.readAsBinaryString(file);
        }

        document.getElementById('dropzone').addEventListener('click', function() {
            document.getElementById('fileInput').click();
        });

        document.getElementById('fileInput').addEventListener('change', function(event) {
            const file = event.target.files[0];
            if (file && file.name.endsWith('.nes')) {
                playROM(file);
            }
        });

        document.getElementById('dropzone').addEventListener('dragover', function(event) {
            event.preventDefault();
            event.stopPropagation();
            event.dataTransfer.dropEffect = 'copy';
        });

        document.getElementById('dropzone').addEventListener('drop', function(event) {
            event.preventDefault();
            event.stopPropagation();
            const file = event.dataTransfer.files[0];
            if (file && file.name.endsWith('.nes')) {
                playROM(file);
            }
        });
    </script>
</body>
</html>
