<script lang="ts">
  import InlineNode from './InlineNode.svelte';
  let { blocks } = $props();
</script>

{#each blocks as block}
  {#if block.type === 'paragraph'}
    {#if block.children && block.children.length > 0}
      <p>
        {#each block.children as child}
          {#if block.children.length === 1 && child.type === 'text' && child.text === ''}
            <br />
          {:else}
            <InlineNode node={child} />
          {/if}
        {/each}
      </p>
    {/if}

  {:else if block.type === 'heading'}
    {#if block.children && block.children.length > 0}
      {#if block.level === 2}
        <h2>{#each block.children as child}{child.text}{/each}</h2>
      {:else if block.level === 3}
        <h3>{#each block.children as child}{child.text}{/each}</h3>
      {:else if block.level === 4}
        <h4>{#each block.children as child}{child.text}{/each}</h4>
      {:else if block.level === 5}
        <h5>{#each block.children as child}{child.text}{/each}</h5>
      {:else if block.level === 6}
        <h6>{#each block.children as child}{child.text}{/each}</h6>
      {/if}
    {/if}

  {:else if block.type === 'list'}
    {#if block.format == 'unordered'}
      {#if block.children && block.children.length > 0}
        <ul>
          {#each block.children as child}
            <li>
              {#if child.type === 'list-item' && child.children && child.children.length > 0}
                {#each child.children as child2}
                  <InlineNode node={child2} />
                {/each}
              {/if}
            </li>
          {/each}
        </ul>
      {/if}
    {:else if block.format == 'ordered'}
      {#if block.children && block.children.length > 0}
        <ol>
          {#each block.children as child}
            <li>
              {#if child.type === 'list-item' && child.children && child.children.length > 0}
                {#each child.children as child2}
                  <InlineNode node={child2} />
                {/each}
              {/if}
            </li>
          {/each}
        </ol>
      {/if}
    {/if}

  {:else if block.type === 'image' && block.image && block.image.url !== ''}
    <img
      alt={block.image.alternativeText ? block.image.alternativeText : ''}
      src={block.image.url}
      class="h-auto w-full"
    />
  {/if}
{/each}