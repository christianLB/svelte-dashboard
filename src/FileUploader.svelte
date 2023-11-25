<script>
  import { onMount } from 'svelte';
  import axios from 'axios';

  let isUploading = false;
  let uploadError = false;
  let errorMessage = '';
  let successMessage = '';
  let isDragActive = false;

  let dropzone; // Referencia al elemento del DOM

  onMount(() => {
    // Inicializa Dropzone
    dropzone.addEventListener('drop', handleDrop);
    dropzone.addEventListener('dragover', () => isDragActive = true);
    dropzone.addEventListener('dragleave', () => isDragActive = false);
  });

  async function handleDrop(event) {
    event.preventDefault();
    isDragActive = false;
    isUploading = true;
    uploadError = false;
    errorMessage = '';
    successMessage = '';

    const acceptedFiles = event.dataTransfer.files;
    const formData = new FormData();
    formData.append('file', acceptedFiles[0]);

    try {
      const response = await axios.post('/api/addExpenseWithMedia', formData, {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
      });
      console.log('Archivo cargado:', response.data);
      successMessage = 'Archivo cargado con éxito.';
      setTimeout(() => {
        successMessage = '';
      }, 3000);
    } catch (error) {
      console.error('Error al cargar archivo:', error);
      uploadError = true;
      errorMessage = 'Error al cargar el archivo.';
    } finally {
      isUploading = false;
    }
  }
</script>

<div
  bind:this={dropzone}
  style={`border: 2px dashed ${uploadError ? 'red' : !isDragActive ? '#EAEAEA' : '#0087F7'};
         padding: 20px;
         text-align: center;
         position: relative;
         height: 100px;`} 
  class="w-1/4"
>
  {#if isUploading}
  garga
  {:else if !uploadError}
    {#if isDragActive}<p>Suelta para agregar</p>{:else}<p>Arrastra para agregar</p>{/if}
    {#if successMessage}<p style="color: green;">{successMessage}</p>{/if}
  {:else}
    <p style="color: red;">{errorMessage}</p>
  {/if}
</div>


<style>
  .loader {
    /* Estilos para el Spinner */
  }
</style>
