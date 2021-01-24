<script>
  import { createEventDispatcher } from "svelte";
  import generateNavigationOptions from "./generateNavigationOptions";
  import { PREVIOUS_PAGE, NEXT_PAGE, ELLIPSIS } from "../src/symbolTypes";

  const dispatch = createEventDispatcher();

  export let totalItems = 0;
  export let pageSize = 1;
  export let currentPage = 1;
  export let limit = null;
  export let showStepOptions = false;

  $: options = generateNavigationOptions({
    totalItems,
    pageSize,
    currentPage,
    limit,
    showStepOptions,
  });

  $: totalPages = Math.ceil(totalItems / pageSize);

  function handleOptionClick(option) {
    dispatch("setPage", { page: option.value });
    document.body.scrollIntoView({ behavior: "smooth" });
  }
</script>

<div class="light-pagination-nav">
  <div class="pagination-nav">
    {#each options as option}
      <span
        class="option"
        class:number={option.type === "number"}
        class:prev={option.type === "symbol" && option.symbol === PREVIOUS_PAGE}
        class:next={option.type === "symbol" && option.symbol === NEXT_PAGE}
        class:disabled={(option.type === "symbol" &&
          option.symbol === NEXT_PAGE &&
          currentPage >= totalPages) ||
          (option.type === "symbol" &&
            option.symbol === PREVIOUS_PAGE &&
            currentPage <= 1)}
        class:ellipsis={option.type === "symbol" && option.symbol === ELLIPSIS}
        class:active={option.type === "number" && option.value === currentPage}
        on:click={() => handleOptionClick(option)}>
        {#if option.type === "number"}
          <slot name="number" value={option.value}>
            <span>{option.value}</span>
          </slot>
        {:else if option.type === "symbol" && option.symbol === ELLIPSIS}
          <slot name="ellipsis"><span>...</span></slot>
        {:else if option.type === "symbol" && option.symbol === PREVIOUS_PAGE}
          <slot name="prev">
            <svg style="width:23px;height:23px" viewBox="0 0 24 24">
              <path
                fill="#36393B"
                d="M15.41,16.58L10.83,12L15.41,7.41L14,6L8,12L14,18L15.41,16.58Z"
              />
            </svg>
          </slot>
        {:else if option.type === "symbol" && option.symbol === NEXT_PAGE}
          <slot name="next">
            <svg style="width:23px;height:23px" viewBox="0 0 24 24">
              <path
                fill="#36393B"
                d="M8.59,16.58L13.17,12L8.59,7.41L10,6L16,12L10,18L8.59,16.58Z"
              />
            </svg>
          </slot>
        {/if}
      </span>
    {/each}
  </div>
</div>

<style>
  .light-pagination-nav :global(.pagination-nav) {
    display: flex;
    justify-content: center;
    background: #fafafa00;
  }
  .light-pagination-nav :global(.option) {
    padding: 0 8px 3px 8px;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: 0.2s all ease-out;
    user-select: none;
    color: rgba(170, 170, 170, 0.5);
    border-bottom: 2px solid rgba(170, 170, 170, 0);
    font-family: Rubik, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
      Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
    font-size: 1.1rem;
  }

  .light-pagination-nav :global(.option.number),
  .light-pagination-nav :global(.option.ellipsis) {
    padding: 5px 12px;
  }
  .light-pagination-nav :global(.option:hover) {
    background: #0000001c;
    cursor: pointer;
    border-radius: 5px;
  }
  .light-pagination-nav :global(.option.active) {
    color: #3b3936;
    border-bottom: 2px solid rgba(170, 170, 170, 0.5);
  }
</style>
