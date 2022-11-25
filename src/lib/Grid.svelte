<script>
    import Square from './Square.svelte'

    // size of grid
    let width = 10;
    let height = 10;
    let noMines = 0;
    let squares = [];
    let surrounding = [];
    let gameover = false;
    let score = 0;

    // percentage of squares that's a mine
    let PERCENTAGE_MINE = 0.3;
    
    $: {
        squares = [];
        surrounding = [];
        noMines = 0;

        for (let i=0; i < width * height; i++) {
            if (Math.random() < PERCENTAGE_MINE) {
                squares.push(1);
                noMines += 1;
            } else {
                squares.push(0);
            }
        }

        for (let i=0; i < width * height; i++) {
            let noNeighbours = 0;

            // neighbouring squares indices
            let neighbours = [
                i - width - 1, i - width, i - width + 1,
                i - 1, i + 1,
                i + width - 1, i + width, i + width + 1];
            
            // check noMines in neighbours
            for (let neighbour of neighbours) {
                if (neighbour >= 0 
                && neighbour < squares.length 
                && squares[neighbour] === 1
                ) {
                    noNeighbours++;
                }
            }

            surrounding.push(noNeighbours);
        }
    }

    function handleGameover(event) {
        gameover = true;
    }

    function checkGameState(e) {
        if (gameover) {
            e.stopPropagation();
        }
    }
    
   
</script>


{#if gameover} 
    <h1>Game over :()</h1>

    <button >
        Try again!
    </button>
{/if}

<div>
    The Grid is {width} by {height}, with {noMines} mines.
</div>

<div>
    <input type="range" id="width" name="width" min="1" max="15" bind:value={width}>
    <label for="width">Width</label>
</div>

<div>
    <input type="range" id="height" name="height" min="1" max="15" bind:value={height}>
    <label for="height">Height</label>
</div>

<div class="grid-mines" 
    style="grid-template-columns: repeat({width}, 2em);
    grid-template-rows: repeat({height}, 2em);">
    {#each squares as square, i}
        <Square isMine={square} surrounding={surrounding[i]} on:gameover={handleGameover} gameState={gameover}/>
    {/each}
</div>

<style>
	div.grid-mines {
		display: grid;
		grid-gap: 1em
	}

    button {
        margin: 2em;
        padding: 2em;
    }

    div {
        margin: 1em;
        padding: 1em;
    }
</style>
