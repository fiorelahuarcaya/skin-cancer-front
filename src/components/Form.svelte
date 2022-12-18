<script lang="ts">
  let fileInput: HTMLInputElement;
  let message = "";
  let predict = 0;
  let melanoma = 0;
  let other = 0;
  let imageUrl = "";

  async function sendData() {
    const formData = new FormData();

    if (!fileInput.files) {
      return;
    }

    const imageFile = fileInput.files[0];

    formData.append("file", imageFile);

    const options = {
      method: "POST",
      body: formData,
    };

    try {
      const res = await fetch("http://localhost:5000/image/", options);
      const data = await res.json();
      console.log(data);
      predict = data.predict;
      melanoma = data.melanoma;
      other = data.other;

      if (predict == 0) {
        message = data.message;
      }
    } catch (error) {
      console.log(error);
      message = "Error al subir la imagen";
    }
  }

  function previewImage() {
    if (!fileInput.files) {
      return;
    }
    const imageFile = fileInput.files[0];
    if (imageFile) {
      imageUrl = URL.createObjectURL(imageFile);
    }
  }
</script>

<div class="form">
  <div class="wrapper">
    <div class="wrapp">
      <div class="upload">
        <label for="file"> Sube tu imagen </label>
        <input
          class="file-upload"
          type="file"
          name="file"
          bind:this={fileInput}
          on:change={previewImage}
        />
      </div>

      {#if imageUrl}
        <div class="view-preview">
          <div class="image">
            <img src={imageUrl} alt="preview" />
            {#if fileInput.files}
              <p>{fileInput.files[0].name}</p>
            {/if}
          </div>
          <input
            class="btn"
            type="submit"
            value="Evaluar"
            on:click={sendData}
          />
        </div>
      {/if}
    </div>

    <div class="result">
      <img src={imageUrl} alt="preview" />
      <div class="" />
    </div>
  </div>
</div>

<style>
  .form {
    background-color: var(--primary-100);
    padding: 48px 0px;
  }

  .wrapper {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  .wrapp {
    display: flex;
    flex-direction: column;
  }

  .upload {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 16px;
    padding: 24px;
  }

  .upload .file-upload {
    border: 2px solid var(--primary-800);
    background-color: var(--primary-200);
    border-style: dashed;
    padding: 64px 24px;
    cursor: pointer;
  }

  .file-upload::file-selector-button {
    visibility: hidden;
  }

  .file-upload::before {
    content: "Arrastra una imagen o haz click aquí";
    display: inline-flex;
    outline: none;
    user-select: none;
    cursor: pointer;
    width: 100%;
    text-align: center;
    justify-content: center;
    align-items: center;
  }

  .view-preview {
    width: 600px;
    height: 100px;
    object-fit: cover;

    background-color: white;

    display: flex;
    align-items: center;
    justify-content: space-between;

    padding: 0px 32px;
  }

  .view-preview .image {
    height: 100%;
    display: flex;
    flex-direction: row;
    gap: 24px;
    align-items: center;
  }

  .view-preview img {
    height: 80%;
  }

  .view-preview .btn {
    bottom: 0;
    right: 0;
    border-radius: 0px;
    color: white;
    border: none;
    background-color: var(--primary-800);
  }

  .view-preview .btn:hover {
    background-color: var(--primary-700);
  }
</style>
