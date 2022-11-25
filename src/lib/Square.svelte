<script>
    // send event of game over to grid
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    // before square is clicked
    let masked = true;
    let isFlag = false;

    export let isMine;
    export let surrounding;
    export let gameState;

    function clickSquare(e) {
        if (gameState === true) {
            return;
        }
        
        if (e.which === 1) {
            if (isFlag) return;

            // turn off mask
            masked = false;
            if (isMine) {
                dispatch("gameover", {});
            }            
        } else if (e.which === 3) {
            isFlag = !isFlag;
        }
    }
</script>


<button on:click|preventDefault={clickSquare} on:contextmenu|preventDefault={clickSquare}>
    {#if masked}
        <div/>
    {:else}
        {#if isMine}
            <img src="rick.jpeg" alt="cat mine" width="30" height="30">
        {:else}
            <div>
                {surrounding}
            </div>    
        {/if}
    {/if}

    {#if isFlag}
        <img src="cat.jpeg" alt="cat mine" width="30" height="30">
    {/if}
</button>

<style>
    button {
        margin: 2;
        padding: 0;
        outline: 0;
        border-radius: 4px;
        background-color: orange;
    }

    button:hover {
        background-color: aquamarine;
        border-color: aqua;
    }
</style>


