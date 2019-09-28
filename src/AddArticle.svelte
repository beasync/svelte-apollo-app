<script>
  import { restore, mutate } from 'svelte-apollo';
  import { client } from './apollo';
  import gql from 'graphql-tag';

  const ARTICLE_LIST = gql`
    query {
      article(order_by: [{title: asc}]) {
        title
      }
    }
  `;
  const ADD_ARTICLE = gql`
    mutation($title: String!, $author_id: Int) {
      insert_article(objects: [{title: $title, author_id: $author_id}]) {
        affected_rows
      }
    }
  `;
  let title = '';
  export let articleCache;
  
  async function addArticle(e) {
    e.preventDefault();
    try {
      await mutate(client, {
        mutation: ADD_ARTICLE,
        variables: { title, author_id: 1 }
      });
      alert("Added successfully");
      const finalData = articleCache.data.article;
      finalData.push({title, '__typename': 'article'});
      restore(client, ARTICLE_LIST, {article: finalData});
      // clear input
      title = '';
    } catch(error) {
      console.error(error);
    }
  }
</script>

<form on:submit={addArticle}>
  <label for="author">Article</label>
  <input type="text" id="article-title" bind:value={title} />
  <button type="submit">Add Article</button>
</form>