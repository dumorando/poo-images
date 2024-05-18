<script>
  let file = null;

  async function handleFileInput(event) {
    file = event.target.files[0];
    const imageObject = await processImage(file);
    saveAsPoo(imageObject);
  }

  async function processImage(file) {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.onload = (event) => {
        const img = new Image();
        img.onload = () => {
          const canvas = document.createElement('canvas');
          const ctx = canvas.getContext('2d');
          canvas.width = img.width;
          canvas.height = img.height;
          ctx.drawImage(img, 0, 0);
          const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
          const pixels = [];
          for (let i = 0; i < imageData.data.length; i += 4) {
            const r = imageData.data[i];
            const g = imageData.data[i + 1];
            const b = imageData.data[i + 2];
            const hex = rgbToHex(r, g, b);
            pixels.push(hex);
          }
          resolve({ width: img.width, height: img.height, pixels });
        };
        img.src = event.target.result;
      };
      reader.readAsDataURL(file);
    });
  }

  function rgbToHex(r, g, b) {
    return '#' + componentToHex(r) + componentToHex(g) + componentToHex(b);
  }

  function componentToHex(c) {
    const hex = c.toString(16);
    return hex.length == 1 ? '0' + hex : hex;
  }

  async function saveAsPoo(imageObject) {
    const fileName = file.name.split('.')[0] + '.poo';
    const content = JSON.stringify(imageObject, null, 2);
    const blob = new Blob([content], { type: 'application/json' });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = fileName;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  }
</script>

<main>
  <input type="file" accept="image/*" on:change={handleFileInput} />
</main>