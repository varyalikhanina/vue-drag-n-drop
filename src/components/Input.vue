<template>
  <div>
    <h1 class="title">File Uploader</h1>
    <p class="subtitle">Загрузите картинку и кликните "Отправить", чтобы увидеть ее URL в формате base64 в консоли.</p>
    <p class="subtitle">Загрузчик не переводит в этот формат URL файлов другого типа.</p>
    <p class="subtitle">Если Вы загрузите несколько картинок, Вы увидите их в консоли, а по клику на "Отправить" - их URL.</p>
    <div class="files">
      <p class="files__text" v-if="files.length === 0">Пока нет загруженных файлов</p>
      <ul class="files__list" v-else-if="files.length !== 0">
        <li class="files__list-item" v-for="(file, idx) in files" :key="idx">{{ file }}</li>
      </ul>
    </div>
    <form class="upload" @submit.prevent="displayUrl" @dragover="addDragOverAnimation" @dragleave="removeDragOverAnimation" @drop="removeDragOverAnimation">
      <p class="upload__message" v-if="!fileUploaded">Перетащите файл сюда</p>
      <input class="upload__input" type="file" name="file" multiple @input="displayImages" />
      <img v-show="fileUploaded" class="upload__image" id="image" alt="Ваша картинка" />
      <button class="upload__button" type="submit">Отправить</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'Input',
  data() {
    return {
      files: [],
      fileUploaded: false
    }
  },
  methods: {
    displayImages(event) {
      const files = Array.from(event.target.files);
      files.forEach(file => {
        if (!this.files.includes(file.name)) {
          this.files.push(file.name);
        }
      });
      if (files.length === 1) {
        const fileFormat = files[0].name.split('.').pop();
        if (fileFormat === 'png' || fileFormat === 'jpeg' || fileFormat === 'jpg' || fileFormat === 'gif') {
          const reader = new FileReader();
          reader.onload = (e) =>  {
            document.getElementById('image').setAttribute('src', e.target.result);
          }
          reader.readAsDataURL(files[0]);
          this.fileUploaded = true;
        } else {
          this.fileUploaded = false;
        }
      } else {
        files.forEach(file => {
          const fileFormat = file.name.split('.').pop();
          if (fileFormat === 'png' || fileFormat === 'jpeg' || fileFormat === 'jpg' || fileFormat === 'gif') {
            const reader = new FileReader();
            reader.onload = (e) =>  {
              (function(url) {
                const image = new Image();
                image.onload = function() {
                  const style = [
                    'font-size: 1px;',
                    'line-height: 100px;',
                    'padding: 300px;',
                    'margin: 10px;',
                    'background-size: contain;',
                    'background-repeat: no-repeat;',
                    `background-image: url(${url});`
                  ].join(' ');
                  console.log('%c ', style);
                };
                image.src = url;
              })(e.target.result);
            }
            reader.readAsDataURL(file);
          }
          this.fileUploaded = false;
        });
      }
    },
    displayUrl(event) {
      const files = event.target.elements.file.files;
      files.forEach((file) => {
        const fileFormat = file.name.split('.').pop();
        if (fileFormat === 'png' || fileFormat === 'jpeg' || fileFormat === 'jpg' || fileFormat === 'gif') {
          const reader = new FileReader();
          reader.onload = (e) =>  {
            console.log(e.target.result);
          }
          reader.readAsDataURL(file);
        } else {
          return;
        }
      });
    },
    addDragOverAnimation(e) {
      const form = e.target.closest('.upload');
      form.style.border = '2px dashed #aaa';
    },
    removeDragOverAnimation(e) {
      const form = e.target.closest('.upload');
      form.style.border = '2px dashed #eee';
    }
  }
}
</script>

<style scoped lang="scss">
h1 {
  font-size: 24px;
  margin: 0 0 20px 0;
}
.subtitle {
  font-size: 14px;
  margin: 0;
  text-align: left;
  max-width: 60%;
  margin: 0 auto 5px;
  &:last-of-type {
    margin: 0 auto 20px auto;
  }
}
.files {
  border: 1px solid #aaa;
  margin: 0 auto 20px;
  .files__text {
    margin: 10px;
  }
  .files__list-item {
    font-size: 12px;
    text-align: left;
  }
}
.upload {
  position: relative;
  display: inline-block;
  width: 300px;
  height: 300px;
  border: 2px dashed #eee;
  cursor: pointer;
  text-align: center;
  padding: 10px;
  transition: .5s ease-in-out;
    .upload__message {
      font-size: 14px;
      text-transform: uppercase;
      margin: 0;
      position: relative;
      top: 50%;
      transform: translateY(-50%);
    }
    .upload__image, .upload__list-wrapper {
      max-width: 100%;
      max-height: 100%;
    }
    .upload__input {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      width: 100%;
      height: 100%;
      cursor: pointer;
      opacity: 0;
    }
    .upload__button {
      position: absolute;
      top: 105%;
      left: 50%;
      transform: translateX(-50%);
      padding: 10px;
      background-color: #fff;
      border: 1px solid #121212;
      font-size: 12px;
      text-transform: uppercase;
      cursor: pointer;
      transition: .2s ease-in-out;
        &:hover {
          background-color: #121212;
          color: #fff;
        }
    }
}
</style>
