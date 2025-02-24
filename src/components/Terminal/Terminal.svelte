<script>
    import { onMount } from "svelte";
    import xterm from "xterm";
    import FitAddon from "xterm-addon-fit";
    import ResizeObserver from "svelte-resize-observer";

    let terminalElement;
    let termFit;
    let terminalController;

    function initializeXterm() {
        terminalController = new xterm.Terminal({
            fontSize: 12,
        });
        termFit = new FitAddon.FitAddon();
        terminalController.loadAddon(termFit);
        terminalController.open(terminalElement);
        termFit.fit();
        terminalController.write("\x1b[32mHello\x1b[m\r\n");
    }

    onMount(async () => {
        initializeXterm();
    });

    function handleResize() {
        if (termFit) {
            termFit.fit();
        }
    }
</script>

<div id="terminal" bind:this={terminalElement} />

<div class="observer">
    <ResizeObserver on:resize={handleResize} />
</div>

<style>
    .observer {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
    }

    /* Xterm.css */
    :global(.xterm) {
        cursor: text;
        position: relative;
        user-select: none;
        -ms-user-select: none;
        -webkit-user-select: none;
    }

    :global(.xterm.focus),
    :global(.xterm:focus) {
        outline: none;
    }

    :global(.xterm .xterm-helpers) {
        position: absolute;
        top: 0;
        /**
			 * The z-index of the helpers must be higher than the canvases in order for
			 * IMEs to appear on top.
			 */
        z-index: 5;
    }

    :global(.xterm .xterm-helper-textarea) {
        padding: 0;
        border: 0;
        margin: 0;
        /* Move textarea out of the screen to the far left, so that the cursor is not visible */
        position: absolute;
        opacity: 0;
        left: -9999em;
        top: 0;
        width: 0;
        height: 0;
        z-index: -5;
        /** Prevent wrapping so the IME appears against the textarea at the correct position */
        white-space: nowrap;
        overflow: hidden;
        resize: none;
    }

    :global(.xterm .composition-view) {
        /* TODO: Composition position got messed up somewhere */
        background: #000;
        color: #fff;
        display: none;
        position: absolute;
        white-space: nowrap;
        z-index: 1;
    }

    :global(.xterm .composition-view.active) {
        display: block;
    }

    :global(.xterm .xterm-viewport) {
        /* On OS X this is required in order for the scroll bar to appear fully opaque */
        background-color: #000;
        overflow-y: scroll;
        cursor: default;
        position: absolute;
        right: 0;
        left: 0;
        top: 0;
        bottom: 0;
    }

    :global(.xterm .xterm-screen) {
        position: relative;
    }

    :global(.xterm .xterm-screen canvas) {
        position: absolute;
        left: 0;
        top: 0;
    }

    :global(.xterm .xterm-scroll-area) {
        visibility: hidden;
    }

    :global(.xterm-char-measure-element) {
        display: inline-block;
        visibility: hidden;
        position: absolute;
        top: 0;
        left: -9999em;
        line-height: normal;
    }

    :global(.xterm.enable-mouse-events) {
        /* When mouse events are enabled (eg. tmux), revert to the standard pointer cursor */
        cursor: default;
    }

    :global(.xterm.xterm-cursor-pointer),
    :global(.xterm .xterm-cursor-pointer) {
        cursor: pointer;
    }

    :global(.xterm.column-select.focus) {
        /* Column selection mode */
        cursor: crosshair;
    }

    :global(.xterm .xterm-accessibility),
    :global(.xterm .xterm-message) {
        position: absolute;
        left: 0;
        top: 0;
        bottom: 0;
        right: 0;
        z-index: 10;
        color: transparent;
    }

    :global(.xterm .live-region) {
        position: absolute;
        left: -9999px;
        width: 1px;
        height: 1px;
        overflow: hidden;
    }

    :global(.xterm-dim) {
        opacity: 0.5;
    }

    :global(.xterm-underline) {
        text-decoration: underline;
    }

    :global(.xterm-strikethrough) {
        text-decoration: line-through;
    }

    :global(.xterm-screen .xterm-decoration-container .xterm-decoration) {
        z-index: 6;
        position: absolute;
    }

    :global(.xterm-decoration-overview-ruler) {
        z-index: 7;
        position: absolute;
        top: 0;
        right: 0;
        pointer-events: none;
    }

    :global(.xterm-decoration-top) {
        z-index: 2;
        position: relative;
    }
</style>
