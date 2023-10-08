<script lang="ts">
    import {Html5Qrcode, type Html5QrcodeResult} from 'html5-qrcode'
    import { onMount } from 'svelte'
    import {base} from "$app/paths";
    import CryptoES from "crypto-es";
    import SaltInput from "$lib/components/SaltInput.svelte";

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
        scanning = true
        html5Qrcode.start(
            { facingMode: 'environment' },
            {
                fps: 5,
                qrbox: { width: 250, height: 250 },
            },
            onScanSuccess,
            onScanFailure
        )

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
    }

    function decrypt_aes() {
        decrypted_data = CryptoES.AES.decrypt(scanned_data, key).toString(CryptoES.enc.Utf8);
    }

    let use_salt: boolean = false;
    let salt: string = "";
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
    <reader hidden="{!scanning}" id="reader"/>
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
        <SaltInput bind:use_salt={use_salt} bind:salt={salt}/>
    {/if}

    {#if decrypted_data}
        <p>{decrypted_data}</p>
    {/if}
</main>