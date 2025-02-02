<script lang="ts">
  import Editor from '@/src/lib/components/Editor/Editor.svelte';
  import * as Tabs from '@/components/ui/tabs';
  import * as ContextMenu from '@/components/ui/context-menu';
  import EditorTitle from '@/components/EditorTitle.svelte';
  import { activeTabId, editors } from '@/store/store';
  import { Notpad } from '@/helpers/notpad';
  import { slide } from 'svelte/transition';

  let innerWidth = window.innerWidth;

  $: isXS = innerWidth <= 450;
  /**
   * Compact mode is disabled on mobile devices (width <= 450px)
   * and on PCs when multiple editors are open.
   */
  $: tabsMode = $editors.length > 1;
</script>

<svelte:window bind:innerWidth />

<Tabs.Root bind:value={$activeTabId} class="flex h-full w-full flex-col overflow-hidden">
  {#if tabsMode || isXS}
    <div transition:slide>
      <Tabs.List
        class="w-full justify-start gap-1
        overflow-x-auto rounded-t-none py-0.5 shadow"
      >
        {#each $editors as editor}
          <Tabs.Trigger asChild value={editor.id} class={tabsMode ? 'pr-1' : ''}>
            <div
              role="button"
              tabindex="0"
              class="flex items-center justify-center rounded-md py-1 pl-2 pr-1
                {$activeTabId === editor.id ? 'bg-background' : 'bg-secondary'}"
              on:click={() => ($activeTabId = editor.id)}
              on:keydown={(e) => {
                if (e.key === 'Enter' || e.key === ' ') {
                  $activeTabId = editor.id;
                }
              }}
            >
              <EditorTitle {editor} />
            </div>
          </Tabs.Trigger>
        {/each}
      </Tabs.List>
    </div>
  {/if}

  {#each $editors as editor (editor.id)}
    <Tabs.Content value={editor.id} class="mt-0 h-full w-full">
      <ContextMenu.Root>
        <ContextMenu.Trigger class="flex h-full w-full flex-col overflow-hidden">
          <Editor {editor} />
        </ContextMenu.Trigger>
        <ContextMenu.Content class="w-48">
          <ContextMenu.Item on:click={() => Notpad.editOptions.undo()}>
            Undo<ContextMenu.Shortcut>Ctrl+Z</ContextMenu.Shortcut>
          </ContextMenu.Item>
          <ContextMenu.Item on:click={() => Notpad.editOptions.redo()}>
            Redo<ContextMenu.Shortcut>Ctrl+Y</ContextMenu.Shortcut>
          </ContextMenu.Item>
          <ContextMenu.Separator />
          <ContextMenu.Item on:click={() => Notpad.editOptions.cut()}>
            Cut<ContextMenu.Shortcut>Ctrl+X</ContextMenu.Shortcut>
          </ContextMenu.Item>
          <ContextMenu.Item on:click={() => Notpad.editOptions.copy()}>
            Copy<ContextMenu.Shortcut>Ctrl+C</ContextMenu.Shortcut>
          </ContextMenu.Item>
          <ContextMenu.Item on:click={() => Notpad.editOptions.paste()}>
            Paste<ContextMenu.Shortcut>Ctrl+V</ContextMenu.Shortcut>
          </ContextMenu.Item>
          <ContextMenu.Separator />
          <ContextMenu.Item on:click={() => Notpad.editOptions.selectAll()}>
            Select All
            <ContextMenu.Shortcut>Ctrl+A</ContextMenu.Shortcut>
          </ContextMenu.Item>
          <ContextMenu.Item on:click={() => Notpad.editOptions.insertDateAndTime()}>
            Time/Date
          </ContextMenu.Item>
        </ContextMenu.Content>
      </ContextMenu.Root>
    </Tabs.Content>
  {/each}
</Tabs.Root>
