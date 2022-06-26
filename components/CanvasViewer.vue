<template>
  <div class="main-canvas">
    <canvas ref="canvas" id="canvas" width="1920" height="1080"></canvas>
  </div>
</template>

<script setup>
const canvas = ref(null);
const size = reactive({ w: 0, h: 0 });
const particles = [];
const max = 800;
const collection = [];

onMounted(() => {
  watch(
    () => useUploadedImages().value,
    (newData) => {
      console.log(newData);
      start();
    },
    { deep: true }
  );

  const ctx = canvas.value.getContext("2d");
  size.w = canvas.value.width;
  size.h = canvas.value.height;

  const loadImage = (imagePath) => {
    return new Promise((resolve, reject) => {
      const img = document.createElement("img");
      img.src = imagePath;
      img.onload = () => {
        resolve(img);
      };
    });
  };

  const loadImages = async () => {
    return new Promise((resolve) => {
      const parray = useUploadedImages().value.map(
        async (imagePath) => await loadImage(imagePath.path)
      );
      Promise.all(parray).then((res) => {
        resolve(res);
      });
    });
  };

  class Particle {
    constructor(image) {
      this.image = image;
      this.reset(image);
    }
    reset() {
      const spd = 15;
      this.x = size.w / 2;
      this.y = size.h / 2;
      this.scale = Math.random() * 50 + 1;
      this.color =
        "hsl(" + ~~(360 * Math.random()) + ", " + 80 + "%, " + 50 + "%)";
      const v1 = Math.random() * spd;
      const v2 = Math.random() * spd;
      this.vx = Math.random() > 0.5 ? v1 : -v1;
      this.vy = Math.random() > 0.5 ? v2 : -v2;
      this.life = 0;
      this.rotation = Math.random() * (Math.PI * 2);
    }
  }

  const startAnimation = (images) => {
    for (let i = 0; i < max; i++) {
      const p = new Particle(images[Math.floor(Math.random() * images.length)]);
      collection.push(p);
    }
    update();
  };

  function update() {
    requestAnimationFrame(update);
    ctx.clearRect(0, 0, size.w, size.h);

    for (let i = 0; i < max; i++) {
      const p = collection[i];
      p.x += p.vx;
      p.y += p.vy;
      p.life++;
      p.rotation += p.vx * 0.01;
      p.scale += 1;
      if (
        p.x < -p.scale ||
        (p.x > size.w + p.scale && p.y < -p.scale) ||
        p.y > size.h + p.scale
      ) {
        p.reset();
      }
      if (p.life > 500) {
        p.reset();
      }
      ctx.save();
      ctx.setTransform(
        Math.cos(p.rotation),
        Math.sin(p.rotation),
        -Math.sin(p.rotation),
        Math.cos(p.rotation),
        p.x,
        p.y
      );
      ctx.drawImage(p.image, 0, 0, 256, 256, 0, 0, p.scale, p.scale);
      ctx.restore();
    }
  }

  const start = () => {
    collection.length = 0;
    loadImages().then((res) => {
      //console.log(res);
      if (res.length !== 0) {
        startAnimation(res);
      }
    });
  };
});
</script>

<style scoped>
#canvas {
  width: auto;
  height: 100vh;
}
</style>
