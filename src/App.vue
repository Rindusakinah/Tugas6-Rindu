<template>
  <div id="app">
    <div class="container">
      <section class="section">
        <h1 style="color: #ff6347;">Posts</h1>
        <ul class="posts">
          <li v-for="post in posts" :key="post.id" class="post-item">
            <div class="post-header">
              <h2>{{ post.title }}</h2>
              <button @click="deletePost(post.id)">Delete</button>
            </div>
            <p>{{ post.content }}</p>
          </li>
        </ul>
        <form @submit.prevent="addPost" class="post-form">
          <input type="text" v-model="newPost.title" placeholder="Enter title">
          <textarea v-model="newPost.content" placeholder="Enter content"></textarea>
          <button type="submit">Submit</button>
        </form>
      </section>

      <section class="section">
        <h1 style="color: #66cdaa;">Comments</h1>
        <ul class="comments">
          <li v-for="comment in comments" :key="comment.id" class="comment-item">
            <div class="comment-header">
              <button @click="deleteComment(comment.id)">Delete</button>
            </div>
            <p>{{ comment.content }}</p>
          </li>
        </ul>
        <form @submit.prevent="addComment" class="comment-form">
          <textarea v-model="newComment.content" placeholder="Add a comment"></textarea>
          <button type="submit">Comment</button>
        </form>
      </section>

      <section class="section">
        <h1 style="color: #4682b4;">Profile</h1>
        <div v-if="profile" class="profile">
          <p><strong>Name:</strong> {{ profile.name }}</p>
          <p><strong>Age:</strong> {{ profile.age }}</p>
          <p><strong>Email:</strong> {{ profile.email }}</p>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  name: 'App',
  setup() {
    const posts = ref([])
    const comments = ref([])
    const profile = ref(null)
    const newPost = ref({ title: '', content: '' })
    const newComment = ref({ content: '' })

    onMounted(async () => {
      try {
        let response = await fetch('http://localhost:3000/posts')
        if (!response.ok) throw new Error(`Error: ${response.status} ${response.statusText}`)
        posts.value = await response.json()

        response = await fetch('http://localhost:3000/comments')
        if (!response.ok) throw new Error(`Error: ${response.status} ${response.statusText}`)
        comments.value = await response.json()

        response = await fetch('http://localhost:3000/profile')
        if (!response.ok) throw new Error(`Error: ${response.status} ${response.statusText}`)
        profile.value = await response.json()
      } catch (error) {
        console.error('Fetch error:', error)
      }
    })

    const addPost = async () => {
      const response = await fetch('http://localhost:3000/posts', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(newPost.value)
      })

      if (response.ok) {
        const postData = await response.json()
        posts.value.push(postData)
        newPost.value = { title: '', content: '' }
      }
    }

    const deletePost = async (postId) => {
      const response = await fetch(`http://localhost:3000/posts/${postId}`, {
        method: 'DELETE'
      })

      if (response.ok) {
        posts.value = posts.value.filter(post => post.id !== postId)
      }
    }

    const addComment = async () => {
      const response = await fetch('http://localhost:3000/comments', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(newComment.value)
      })

      if (response.ok) {
        const commentData = await response.json()
        comments.value.push(commentData)
        newComment.value = { content: '' }
      }
    }

    const deleteComment = async (commentId) => {
      const response = await fetch(`http://localhost:3000/comments/${commentId}`, {
        method: 'DELETE'
      })

      if (response.ok) {
        comments.value = comments.value.filter(comment => comment.id !== commentId)
      }
    }

    return {
      posts,
      comments,
      profile,
      newPost,
      newComment,
      addPost,
      deletePost,
      addComment,
      deleteComment
    }
  }
}
</script>

<style scoped>
body {
  font-family: 'Arial', sans-serif;
  background-color: #f0f0f0;
  margin: 0;
  padding: 20px;
}

#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.container {
  max-width: 800px;
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
}

.section {
  margin-bottom: 40px;
}

h1 {
  font-size: 24px;
  font-weight: bold;
  padding-bottom: 10px;
  border-bottom: 2px solid;
}

ul {
  list-style-type: none;
  padding: 0;
}

.posts, .comments {
  margin: 0;
  padding: 0;
}

.post-item, .comment-item {
  background-color: #fafafa;
  margin-bottom: 20px;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.1);
}

.post-header, .comment-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.post-item h2 {
  margin: 0 0 10px;
  color: #333;
}

.profile p {
  margin: 10px 0;
}

form {
  margin-top: 20px;
}

form textarea, form input[type="text"] {
  width: calc(100% - 22px);
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-family: 'Arial', sans-serif;
}

form button {
  padding: 10px 20px;
  background-color: #4682b4;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

form button:hover {
  background-color: #36648b;
}
</style>
