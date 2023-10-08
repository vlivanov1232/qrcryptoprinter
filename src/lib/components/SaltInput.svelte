<script lang="ts">
    export let use_salt: boolean = false;
    export let salt: string = '';
</script>


<label>
    <input type="checkbox" bind:checked={use_salt}/>
    Use custom salt?
</label>

{#if use_salt}
    <input type="password" bind:value={salt} placeholder="enter salt"/>
{/if}