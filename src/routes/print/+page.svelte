<script lang="ts">
    import {base} from '$app/paths';
    import {AES} from 'crypto-es/lib/aes';
    import QrCode from "./QrCode.svelte";

    let key: string;
    let payload: string;
    let encrypted_payload: string;

    function print_payload() {
        encrypted_payload = AES.encrypt(payload, key).toString();
        console.log(encrypted_payload);
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
</style>

<main>
    <input type="text" bind:value={payload} placeholder="enter payload">
    <input type="password" bind:value={key} placeholder="enter key"/>
    <button on:click={print_payload}>Print</button>
    {#if encrypted_payload}
        <QrCode value={encrypted_payload}/>
    {/if}
    <a href="{base}/">Start page</a>
</main>
