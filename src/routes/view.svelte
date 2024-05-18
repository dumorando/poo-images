<script>
  let file = null;
  let imageObject = null;

  async function handleFileInput(event) {
    file = event.target.files[0];
    imageObject = await readPooFile(file);
    drawImageFromPoo(imageObject);
  }

  async function readPooFile(file) {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.onload = (event) => {
        try {
          const imageObject = JSON.parse(event.target.result);
          resolve(imageObject);
        } catch (error) {
          reject(error);
        }
      };
      reader.readAsText(file);
    });
  }

  function drawImageFromPoo(imageObject) {
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    canvas.width = imageObject.width;
    canvas.height = imageObject.height;
    const imageData = ctx.createImageData(imageObject.width, imageObject.height);
    let dataIndex = 0;
    for (let i = 0; i < imageObject.pixels.length; i++) {
      const color = imageObject.pixels[i];
      const [r, g, b] = hexToRgb(color);
      imageData.data[dataIndex++] = r;
      imageData.data[dataIndex++] = g;
      imageData.data[dataIndex++] = b;
      imageData.data[dataIndex++] = 255;
    }
    ctx.putImageData(imageData, 0, 0);
  }

  function hexToRgb(hex) {
    const bigint = parseInt(hex.substring(1), 16);
    const r = (bigint >> 16) & 255;
    const g = (bigint >> 8) & 255;
    const b = bigint & 255;
    return [r, g, b];
  }
</script>

<main>
  <input type="file" accept=".poo" on:change={handleFileInput} />
  <canvas id="canvas"></canvas>
</main>