<script lang="ts">
    import {Html5Qrcode, type Html5QrcodeResult} from 'html5-qrcode'
    import { onMount } from 'svelte'
    import {base} from "$app/paths";
    import CryptoES from "crypto-es";

    let scanning = false

    let html5Qrcode: Html5Qrcode

    let scanned_data: string

    let key: string;

    let decrypted_data: string;

    onMount(init)

    function init() {
        html5Qrcode = new Html5Qrcode('reader')
    }

    function start() {

        html5Qrcode.start(
            { facingMode: 'environment' },
            {
                fps: 10,
                qrbox: { width: 250, height: 250 },
            },
            onScanSuccess,
            onScanFailure
        )
        scanning = true
    }

    async function stop() {
        await html5Qrcode.stop()
        scanning = false
    }

    function onScanSuccess(decodedText: string, decodedResult: Html5QrcodeResult) {
        scanned_data = decodedText
        console.log(decodedResult)
        scanning = false;
    }

    function onScanFailure(error: string) {
        console.warn(`Code scan error = ${error}`)
        scanning = false;
    }

    function decrypt_aes() {
        decrypted_data = CryptoES.AES.decrypt(scanned_data, key).toString(CryptoES.enc.Utf8);
    }
</script>

<style>
    main {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        gap: 20px;
    }
    reader {
        width: 100%;
        min-height: 500px;
        background-color: black;
    }
</style>

<main>
    {#if scanning}
           <reader id="reader"/>
    {/if}
    {#if scanning}
        <button on:click={stop}>stop</button>
    {:else}
        <button on:click={start}>start</button>
    {/if}
    {#if scanned_data}
        <input type="password" bind:value={key} placeholder="enter key"/>
    {/if}

    {#if key}
        <button on:click={decrypt_aes}>Decrypt</button>
    {/if}

    {#if decrypted_data}
        <p>{decrypted_data}</p>
    {/if}

    <a href="{base}/">Start page</a>
</main>