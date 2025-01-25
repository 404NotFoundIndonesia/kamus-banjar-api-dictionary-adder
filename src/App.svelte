<script>
import Form from "./lib/Form.svelte";

let files;
let object;
let fileName;
let letter;

let formRef;

const onLoadFile = () => {
  const file = files[0];
  if (!file)
    return;

  fileName = file.name;
  letter = fileName?.replace('.json', '');

  const reader = new FileReader();
  reader.onload = () => {
    try {
      object = JSON.parse(reader.result.toString());
    } catch (err) {
      alert('File gak valid, coy');
    }
  };
  reader.readAsText(file);
};

const onSaveFile = () => {
  const writeable = JSON.stringify(object);
  if(!window['showSaveFilePicker']) {
    window.showSaveFilePicker({
        suggestedName: fileName,
        types: [
          {
            description: "JSON Files",
            accept: { "application/json": [".json"] },
          },
        ],
      })
      .then((fileHandle) => {
        // Membuat writable stream
        return fileHandle.createWritable().then((writableStream) => {
          // Menulis file ke writable stream
          writableStream
            .write(writeable)
            .then(() => {
              writableStream.close();
              onClearFile();
            })
            .catch((err) => console.error("Error writing to file:", err));
        });
      })
      .catch((err) => {
        if (err.name !== "AbortError") {
          console.error("Error picking file:", err);
        }
      });
  } else {
    const blob = new Blob([JSON.stringify(writeable)], { type: "application/json" });

    const url = URL.createObjectURL(blob);
    const link = document.createElement("a");
    link.href = url;
    link.download = fileName;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
    URL.revokeObjectURL(url);
    onClearFile();
  }
};

const onClearFile = () => {
  files = null;
  object = null;
  fileName = null;
  letter = null;
};

const openForm = (word, index) => {
  formRef.open(word, index);
};

const onSubmitForm = (word, index) => {
  if (index) {
    object[index] = word;
  } else {
    object.push(word);
  }
};
</script>

<div class="pt-5">
  <div class="card mx-auto w-75">
    <div class="card-body t-5">
      <div class="mb-5">
        <input class="form-control" type="file" bind:files on:change={onLoadFile} accept="application/json">
        <small class="text-secondary">Buka file <span class="badge rounded-pill text-bg-light">.json</span> sesuai abjad yang ingin diubah</small>
      </div>
  
      
      {#if object}
      <div class="row">
        <div class="col-6">
          <div class="mb-3">
            <div class="d-flex justify-content-between mb-2">
              <small>Huruf {letter}</small>
              <small>{object.length} kata</small>
            </div>
            {#each object as word, index}
              <span class="badge rounded-pill text-bg-success cursor-pointer me-1" on:click={() => openForm(word, index)}>{word.word}</span>
            {/each}
            <span class="badge rounded-pill text-bg-light cursor-pointer me-1" on:click={() => openForm(null, null)}>+</span>
          </div>
          <Form 
            added={onSubmitForm}
            bind:this={formRef} />
        </div>
        <div class="col-6">
          <div class="card" style="height: 400px;">
            <div class="card-body overflow-scroll">
              <pre>{JSON.stringify(object, null, 2)}</pre>
            </div>
          </div>
        </div>
      </div>
      {/if}
      
      <div class="mt-5">
        <button disabled={!fileName} type="button" on:click={onSaveFile} class="btn btn-sm btn-primary">
          Simpan
        </button>
        <button disabled={!fileName} type="button" on:click={onClearFile} class="btn btn-sm btn-danger">
          Bersihkan
        </button>
      </div>
    </div>
  </div>
</div>

<style>
  .cursor-pointer {
    cursor: pointer;
  }
</style>