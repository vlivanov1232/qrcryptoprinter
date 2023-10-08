<script lang="ts">
    import CryptoES from 'crypto-es'
    import QrCode from "$lib/components/QrCode.svelte";
    import SaltInput from "$lib/components/SaltInput.svelte";

    let key: string = '';
    let payload: string = '';
    let encrypted_payload: string;

    let use_salt: boolean = false;
    let salt: string = '';

    $: encode_active = key && payload;
    $: active_class = !encode_active ? 'opacity-50 cursor-not-allowed' : '';

    function print_payload() {
        let local_key = key;
        if (use_salt) {
            const key512Bits1000Iterations = CryptoES.PBKDF2(local_key, salt, {keySize: 512 / 32, iterations: 1000});
            local_key = key512Bits1000Iterations.toString();
        }
        encrypted_payload = CryptoES.AES.encrypt(JSON.stringify(payload), local_key).toString();
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

    .btn {
        @apply font-bold py-2 px-4 rounded;
    }

    .btn-blue {
        @apply bg-blue-500 text-white;
    }

    .btn-blue:hover {
        @apply bg-blue-700;
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
    <SaltInput bind:use_salt={use_salt} bind:salt={salt}/>
    <button class='btn btn-blue {active_class}' disabled='{!(encode_active)}' on:click={print_payload}>Encode</button>
    {#if encrypted_payload}
        <div id="section-to-print">
            <QrCode value={encrypted_payload}/>
            <p>{encrypted_payload}</p>
        </div>
    {/if}
</main>
