<script lang="ts">
    import { ContextMenuDivider, ContextMenuOption } from "carbon-components-svelte";
    import ContextMenu from "../ContextMenu/ContextMenu.svelte";
    import RenameModel from "../Modal/RenameModel.svelte";
    import CopyFile from "carbon-icons-svelte/lib/CopyFile.svelte";
    import Cut from "carbon-icons-svelte/lib/Cut.svelte";
    import Script from "carbon-icons-svelte/lib/Script.svelte";
    import { fs, clipboard, shell, path } from "@tauri-apps/api";
    export let target;
    export let filename;
    export let filepath;
    let open;

    async function copyToClipboard(input) {
        await clipboard.writeText(input);
    }
</script>

<ContextMenu {target}>
    <ContextMenuOption labelText="Edit">
        <ContextMenuOption indented icon={CopyFile} labelText="Copy">
            <span class="contextshortcut" slot="shortcutText">Ctrl + C</span>
        </ContextMenuOption>
        <ContextMenuOption indented icon={Cut} labelText="Cut">
            <span class="contextshortcut" slot="shortcutText">Ctrl + X</span>
        </ContextMenuOption>
        <ContextMenuOption indented labelText="Rename..." on:click={() => {open = true}}>
            <span class="contextshortcut" slot="shortcutText">F2</span>
        </ContextMenuOption>
        <ContextMenuOption indented icon={Script} labelText="Edit {filename}"></ContextMenuOption>
    </ContextMenuOption>
    <ContextMenuOption labelText="Copy Path...">
        <!--TODO: Make this relative instead of file name. Am lazy-->
        <ContextMenuOption on:click={() => copyToClipboard(filename)}>
            <span slot="labelText" title={filename}>Copy File Name</span>
        </ContextMenuOption>
        <ContextMenuOption on:click={() => copyToClipboard(filepath)}>
            <span slot="labelText" title={filepath}>Copy Absolute Path</span>
        </ContextMenuOption>
    </ContextMenuOption>
    <ContextMenuOption labelText="Show in Explorer" on:click={() => {
        let dir = filepath.split(path.sep);
        dir.pop();
        dir = dir.join(path.sep);
        shell.open(dir);
    }}></ContextMenuOption>

    <ContextMenuDivider></ContextMenuDivider>
    <ContextMenuOption labelText="Delete..." on:click={ async() => {
        await fs.removeFile(filepath);
    }}>
        <span class="contextshortcut" slot="shortcutText">Delete</span>
    </ContextMenuOption>
</ContextMenu>

<RenameModel bind:open bind:filename={filename} bind:path={filepath} />

<style>
    .contextshortcut {
        color: #8c8c8c;
    }
</style>