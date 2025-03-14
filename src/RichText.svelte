<script lang="ts">
	let { blocks } = $props();

	console.log(blocks);
</script>

{#each blocks as block}
	{#if block.type === 'paragraph'}
		{#if block.children && block.children.length > 0}
			<p>
				{#each block.children as child}
					{#if block.children.length === 1 && child.type === 'text' && child.text === ''}
						<br />
					{:else if child.type === 'text'}
						{#if child.bold}
							<strong>{@html child.text.replace('\n', '<br />')}</strong>
						{:else if child.italic}
							<em>{@html child.text.replace('\n', '<br />')}</em>
						{:else}
							{@html child.text.replace('\n', '<br />')}
						{/if}
					{:else if child.type === 'link'}
						<a href={child.url} title={child.children[0].text}>
							{child.children[0].text}
						</a>
					{/if}
				{/each}
			</p>
		{/if}
	{:else if block.type === 'heading'}
		{#if block.level === 2}
			{#if block.children && block.children.length > 0}
				<h2>
					{#each block.children as child}
						{child.text}
					{/each}
				</h2>
			{/if}
		{:else if block.level === 3}
			{#if block.children && block.children.length > 0}
				<h3>
					{#each block.children as child}
						{child.text}
					{/each}
				</h3>
			{/if}
		{/if}
	{:else if block.type === 'list'}
		{#if block.format == 'unordered'}
			{#if block.children && block.children.length > 0}
				<ul>
					{#each block.children as child}
						<li>
							{#if child.type === 'list-item'}
								{#if child.children && child.children.length > 0}
									{#each child.children as child2}
										{#if child2.bold}
											<strong>{child2.text}</strong>
										{:else if child2.italic}
											<em>{child2.text}</em>
										{:else}
											{child2.text}
										{/if}
									{/each}
								{/if}
							{/if}
						</li>
					{/each}
				</ul>
			{/if}
		{:else if block.format == 'ordered'}
			<ol>
				{#each block.children as child}
					<li>
						{#if child.type === 'list-item'}
							{#if child.children && child.children.length > 0}
								{#each child.children as child2}
									{#if child2.bold}
										<strong>{child2.text}</strong>
									{:else if child2.italic}
										<em>{child2.text}</em>
									{:else}
										{child2.text}
									{/if}
								{/each}
							{/if}
						{/if}
					</li>
				{/each}
			</ol>
		{/if}
	{:else if block.type === 'image' && block.image && block.image.url !== ''}
		<div class="px-8 lg:px-[1.7578125vw]">
			<img
				alt={block.image.alternativeText ? block.image.alternativeText : ''}
				src={block.image.url}
				class="h-auto w-full"
			/>
		</div>
	{/if}
{/each}
