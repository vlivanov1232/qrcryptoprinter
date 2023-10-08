<script lang="ts">
    import {base} from '$app/paths';
    import CryptoES from 'crypto-es'
    import QrCode from "./QrCode.svelte";

    let key: string;
    let payload: string;
    let encrypted_payload: string;

    function print_payload() {
        encrypted_payload = CryptoES.AES.encrypt(JSON.stringify(payload), key).toString();
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

    @media print {
      main {
        visibility: hidden;
      }
      #section-to-print {
        visibility: visible;
        position: absolute;
        left: 0;
        top: 0;
      }
}
</style>

<main>
    <input type="text" bind:value={payload} placeholder="enter payload">
    <input type="password" bind:value={key} placeholder="enter key"/>
    <button disabled='{!(payload && key)}' on:click={print_payload}>Encode</button>
    {#if encrypted_payload}
        <div id="section-to-print">
            <QrCode value={encrypted_payload}/>
            <p>{encrypted_payload}</p>
        </div>
    {/if}
    <a href="{base}/">Start page</a>
</main>
