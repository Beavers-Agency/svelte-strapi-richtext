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

  // URL helper (gère relatif/absolu, protocoles spéciaux)
  function parseUrl(href: string): URL | null {
    try {
      // base = location.origin pour résoudre les liens relatifs
      return new URL(href, globalThis?.location?.origin);
    } catch {
      return null;
    }
  }

  function isExternalLink(href: string): boolean {
    const u = parseUrl(href);
    if (!u) return false;

    // Ne pas cibler en _blank les protocoles spéciaux
    if (u.protocol === 'mailto:' || u.protocol === 'tel:') return false;

    const currentHost = globalThis?.location?.hostname;
    if (!currentHost) return false;

    return u.hostname !== currentHost;
  }
</script>

{#if node?.type === 'text'}
  {@html renderStyledText(node)}

{:else if node?.type === 'link'}
  {#if isExternalLink(node.url)}
    <a href={node.url} target="_blank" rel="noopener" title={linkTitle(node)}>
      {#if node.children && node.children.length > 0}
        {#each node.children as child}
          <svelte:self node={child} />
        {/each}
      {/if}
    </a>
  {:else}
    <a href={node.url} title={linkTitle(node)}>
      {#if node.children && node.children.length > 0}
        {#each node.children as child}
          <svelte:self node={child} />
        {/each}
      {/if}
    </a>
  {/if}

{:else}
  <!-- Fallback (si type inattendu) : tente d'afficher du texte brut -->
  {#if node?.text}
    {@html nl2br(node.text)}
  {/if}
{/if}