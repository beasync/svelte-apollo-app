<script context="module">
  import gql from 'graphql-tag';
  import { client } from './apollo';

  import { subscribe } from 'svelte-apollo';

  const ARTICLE_LIST = gql`
    subscription {
      article(order_by: [{title: asc}]) {
        title
        author {
          name
        }
      }
    }
  `;
  const articlesList = subscribe(client, { query: ARTICLE_LIST });
</script>

<ul>
  {#await $articlesList}
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

