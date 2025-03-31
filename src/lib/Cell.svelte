<script lang="ts">
    import { writable } from "svelte/store";

    export let row: number;
    export let col: number;
    export let content: string = '?';
    export let onClick: (row: number, col: number) => void;
    export let addDisabledCount: (incrementValue: number) => void;

    export let isDisabled = false;

    export let done = writable(false);

    let buttonElement;

    export function getButtonElement() {
        return buttonElement;
    }

    export function getIsDisabled() {
        return isDisabled
    }

    export function declareDone() {
        done.set(true)
        console.log(done)
    }


    function handleClick() {
        disableSelf()
        if (onClick) onClick(row, col);
    }

    function disableSelf() {
        const selfElement = document.getElementById(`cell-${row}-${col}`);
        if (selfElement) {
            isDisabled = !isDisabled
            if (isDisabled) {
                addDisabledCount(1)
            } else {addDisabledCount(-1)}
        }
    }
</script>

<button
    id="cell-{row}-{col}"
    on:click={handleClick}
    class="w-48 h-32 bg-transparent inter focus:outline-none  {!isDisabled ? 'text-gray-600' : 'text-gray-400'} {!$done ? 'italic' : 'font-bold text-slate-950'} text-xl "
    bind:this={buttonElement}
>
    {!isDisabled ? content : "·"}
</button>
