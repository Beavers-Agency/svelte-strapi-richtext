<script lang="ts">
  export let node: any;

  const nl2br = (s: string) => (s ?? '').replace(/\n/g, '<br />');

  // Utilitaires d'affichage du texte avec styles
  function renderStyledText(textNode: any) {
    const html = nl2br(textNode.text ?? '');
    if (textNode.bold)   return `<strong>${html}</strong>`;
    if (textNode.italic) return `<em>${html}</em>`;
    if (textNode.strikethrough) return `<s>${html}</s>`;
    return html;
  }

  // Récupération du "title" du lien (fallback sur la concat des textes enfants)
  function linkTitle(n: any): string {
    if (!n?.children) return '';
    const texts = n.children
      .filter((c: any) => c?.type === 'text' && (c.text ?? '') !== '')
      .map((c: any) => c.text);
    return texts.join(' ').trim();
  }
</script>

{#if node?.type === 'text'}
  {@html renderStyledText(node)}

{:else if node?.type === 'link'}
  <a href={node.url} title={linkTitle(node)} rel="noopener">
    {#if node.children && node.children.length > 0}
      {#each node.children as child}
        <!-- récursion sur les enfants du lien -->
        <svelte:self node={child} />
      {/each}
    {/if}
  </a>

{:else}
  <!-- Fallback (si type inattendu) : tente d'afficher du texte brut -->
  {#if node?.text}
    {@html nl2br(node.text)}
  {/if}
{/if}