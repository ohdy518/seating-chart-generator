<script lang="ts">
    import Cell from '$lib/Cell.svelte';
    import {browser} from "$app/environment";
    import {writable} from "svelte/store";
    import { page } from '$app/state'

    export let width: number = parseInt(page.url.searchParams.get('w'));
    export let height: number = parseInt(page.url.searchParams.get('h'));

    let disabledCount = 0
    let locked = false
    const done = writable(false)

    $: cellArray = []

    $: console.log(cellArray)

    let contentArray = []

    let tempArray = []
    for (let i = 0; i < 10000; i++) {
        tempArray.push(i)
    }
    contentArray = tempArray

    function handleCellClick(row: number, col: number) {
        console.log(`Cell ${row}-${col} clicked`);
    }

    function addDisabledCount(incrementValue: number) {
        disabledCount += incrementValue;
        console.log(disabledCount)
    }

    function addZeroAtIndex(array, index: number) {
        return [
            ...array.slice(0, index),
            0,
            ...array.slice(index)
        ];
    }

    /**
     * Generates an array of length n containing a random permutation of numbers 1 through n.
     * @param {number} n - The size of the array.
     * @returns {number[]} A randomly shuffled array of numbers from 1 to n.
     */
    function createRandomArray(n: number) {
        // Create an array [1, 2, ..., n]
        const arr = Array.from({ length: n }, (_, i) => i + 1);

        // Shuffle the array using Fisher-Yates algorithm
        for (let i = arr.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [arr[i], arr[j]] = [arr[j], arr[i]];
        }

        return arr;
    }

    function lockAll() {
        locked = true
        console.log(cellArray)
        for (let i = 0; i < cellArray.length; i++) {
            console.log(cellArray[i].getIsDisabled());
            cellArray[i].getButtonElement().disabled = true
        }
    }

    function assignArray() {
        if (locked) return
        let randomArray = createRandomArray(cellArray.length - disabledCount)
        for (let i = 0; i < cellArray.length; i++) {
            if (cellArray[i].getIsDisabled()) {
                randomArray = addZeroAtIndex(randomArray, i)
            }
        }
        contentArray = randomArray
        done.set(true)
        for (let i = 0; i < cellArray.length; i++) {
            cellArray[i].declareDone()
        }
    }
</script>
<div class="flex flex-col w-screen h-screen items-center justify-center gap-y-6 bg-gray-100">
    <table class="border-collapse border {!$done?'border-gray-400':'border-slate-950'} rounded-lg bg-white">
        <tbody>
        {#each Array(height) as _, rowIndex}
        <tr class="h-16 border rounded-lg {!$done?'border-gray-400':'border-slate-950'}">
            {#each Array(width) as _, colIndex}
            <td class="w-16 border-2 {!$done?'border-gray-400':'border-slate-700'} rounded-lg text-black">
                <Cell
                    bind:this={cellArray[rowIndex * width + colIndex]}
                    row={rowIndex}
                    col={colIndex}
                    content={contentArray[rowIndex * width + colIndex].toString()}
                    onClick={handleCellClick}
                    addDisabledCount={addDisabledCount}
                />
            </td>
            {/each}
        </tr>
        {/each}
        </tbody>
    </table>
    <div class="flex flex-row gap-3 items-center">
        <button on:click={assignArray} class="inter text-lg px-3 py-1 border-2 border-slate-700 text-slate-700 font-bold bg-white">Assign</button>
        <button on:click={lockAll} class="inter text-lg px-3 py-1 border-2 border-slate-700 text-slate-700 font-bold {locked?'bg-gray-300':'bg-white'}">{!locked ? 'Lock' : 'Locked'}</button>
    </div>
</div>