<script context="module">
  import gql from 'graphql-tag';
  import { client } from './apollo';

  const ARTICLES = gql`
    {
      article {
        id
        title
        author {
          id
          name
        }
      }
    }
  `;
  export async function preload() {
    return {
      articleCache: await client.query({ query: ARTICLES })
    };
  }
</script>

<script>
  import { restore, query } from 'svelte-apollo';
  
  export let articleCache;
  restore(client, ARTICLES, articleCache.data);

  const articles = query(client, { query: ARTICLES });
</script>

<ul>
  {#await $articles}
    <li>Loading...</li>
  {:then result}
    {#each result.data.article as article (article.id)}
      <li>{article.title} - {article.author.name}</li>
    {:else}
      <li>No articles found</li>
    {/each}
  {:catch error}
    <li>Error loading articles: {error}</li>
  {/await}
</ul>