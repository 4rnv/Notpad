<script context="module">
  import { get, writable } from 'svelte/store';

  const open = writable(false);

  export function toggleGoToDialog() {
    open.set(!get(open));
  }
</script>

<script lang="ts">
  import { Button } from '@/components/ui/button';
  import * as Dialog from '@/components/ui/dialog';
  import { Input, type FormInputEvent } from '@/components/ui/input';
  import Label from '@/components/ui/label/label.svelte';
  import { Notpad } from '@/helpers/notpad';

  let line: string | undefined;
  let column: string | undefined;

  function submitGoTo() {
    open.set(false);
    Notpad.searchOptions.goTo({
      line: line ? parseInt(line) : undefined,
      column: column ? parseInt(column) : undefined
    });
  }

  function validateInput(e: FormInputEvent<InputEvent>) {
    // If the input is invalid, reset the value to an empty string

    if (!(e.target as HTMLInputElement).validity.valid) {
      (e.target as HTMLInputElement).value = '';
    }
  }

  $: if (!$open) Notpad.editors.focus();
</script>

<Dialog.Root open={$open} onOpenChange={open.set} preventScroll={false}>
  <Dialog.Content
    overlayClass="bg-transparent backdrop-blur-[.8px]"
    class="top-14 translate-y-0 bg-neutral-100 dark:bg-neutral-900"
  >
    <Dialog.Title>Go To</Dialog.Title>
    <Dialog.Description class="flex gap-3">
      <div class="flex w-full max-w-sm flex-col gap-1.5">
        <Label for="line">Line</Label>
        <Input
          bind:value={line}
          type="number"
          id="line"
          placeholder="Line"
          on:input={validateInput}
          step={1}
          min={1}
        />
      </div>

      <div class="flex w-full max-w-sm flex-col gap-1.5">
        <Label for="column">Column</Label>
        <Input
          bind:value={column}
          type="number"
          id="column"
          placeholder="Column"
          on:input={validateInput}
          step={1}
          min={0}
        />
      </div>

      <Button type="button" class="mt-auto" on:click={submitGoTo}>Go</Button>
    </Dialog.Description>
  </Dialog.Content>
</Dialog.Root>
