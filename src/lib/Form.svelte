<script>
    import { Modal } from "bootstrap";
    import { onMount } from "svelte";

    const word = {
        word: '',
        syllables: '',
        alphabet: '',
        meanings: [],
        derivatives: [],
    };

    const meaning = {
        definitions: [],
    };

    const definition = {
        definition: '',
        refer: '',
        partOfSpeech: '',
        examples: [],
    };

    const example = {
        bjn: '',
        id: '',
    };

    const derivative = {
        word: '',
        syllables: '',
        definitions: [],
    };
    
    let { added } = $props();

    let modal;

    let selectedWord = $state(word);
    let selectedIndex = $state(null);
    export const open = (w, i) => {
        if (w && i !== null) {
            selectedWord = w;
            selectedIndex = i;
        }

        modal?.show();
    };

    const submit = () => {
        added(selectedWord, selectedIndex);
        modal.hide();
    };

    onMount(() => {
        const element = document.querySelector('#form-modal.modal');
        modal = new Modal(element);
        element.addEventListener('hidden.bs.modal', e => {
            selectedWord = word;
            selectedIndex = null;
        });
    });
</script>

<div class="modal" tabindex="-1" id="form-modal">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">{selectedWord?.['word'] ?? 'Kosakata Baru'}</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
                <div class="mb-3">
                    <label for="formWord" class="form-label">Kosakata</label>
                    <input type="text" class="form-control" id="formWord" bind:value={selectedWord.word}>
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-sm btn-secondary" data-bs-dismiss="modal">Tutup</button>
                <button type="button" class="btn btn-sm btn-primary" on:click={submit}>Tambahkan</button>
            </div>
        </div>
    </div>
</div>