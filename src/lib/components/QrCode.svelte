<script>
    import QRCode from 'qrcode';
    import {onMount} from "svelte";

    let canvas;

    export let value = '';
    export let width = '200';
    export let errorCorrectionLevel = 'M';


    const options = {
        errorCorrectionLevel,
        width,

    };

    onMount(() => {
        QRCode.toCanvas(canvas, value, options, function (error) {
            if (error) console.error(error);
        })
    });

</script>

<canvas bind:this={canvas}></canvas>