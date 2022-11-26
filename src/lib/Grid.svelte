<script>
    import Square from './Square.svelte'
    import { flagged } from './flagged';
    import { unmasked } from './masked';

    // size of grid
    let width = 10;
    let height = 10;

    // no mines on board
    let noMines = 0;

    // keeps track of the squares, 1 is mine, 0 is safe
    let squares = [];
    // keeps track of the no mines in the neighbouring squares
    let surrounding = [];

    let gameover = false;

    // no of correct flags on mines
    let score = 0;
    // no of incorrect flags
    let wrongScore = 0;

    // percentage of squares that's a mine
    let PERCENTAGE_MINE = 0.3;
    
    $: {
        squares = [];
        surrounding = [];
        noMines = 0;     
        $unmasked = [];   

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

        for (let index of $flagged) {
            if (squares[index] === 1) {
                score++;
            } else {
                wrongScore++;
            }                
        }
    }    


    function reset() {
        gameover = false;

        $unmasked = [];
        $flagged = [];
    }
   
</script>

{$unmasked}
{$flagged}

{#if gameover} 
    <h1>Game Over :()</h1>

    <h2>You flagged {score} out of {noMines} mines!</h2>

    {#if wrongScore != 0} 
        <h2>You wrongly flagged {wrongScore} safe squares as mines.</h2>
    {/if}

    <button on:click={reset}>
        Try again!
    </button>
{/if}

{#if score===noMines}
    <h1>You WON!!!</h1>

    <button on:click={reset}>
        Another!
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
        <Square 
        on:gameover={handleGameover} 
        isMine={square} 
        surrounding={surrounding[i]} 
        gameState={gameover}
        id={i}/>
    {/each}
</div>

<style>
	div.grid-mines {
		display: grid;
		grid-gap: 1em
	}

    button {
        padding: 2em;
    }

    div {
        margin: 1em;
        padding: 1em;
    }
</style>
