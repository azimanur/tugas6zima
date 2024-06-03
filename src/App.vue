<template>
  <div class="container">
    <form @submit.prevent="save" class="form">
      <h2>Artikel</h2>
      <input
        type="text"
        v-model="form.title"
        placeholder="Judul"
        class="input"
      />
      <textarea
        v-model="form.content"
        placeholder="Isi Konten..."
        class="textarea"
      ></textarea>
      <button type="submit" class="button save-button">Save</button>
    </form>
    <button @click="load" class="button load-button">Load Articles</button>
    <div class="articles-section">
      <ul class="article-list">
        <li v-for="article in articles" :key="article.id" class="article-item">
          <div class="article-content">
            <h3>{{ article.title }}</h3>
            <p>{{ article.content }}</p>
          </div>
          <div class="button-group">
            <button @click="edit(article)" class="button edit-button">
              Edit
            </button>
            <button @click="remove(article.id)" class="button delete-button">
              Delete
            </button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { ref, reactive, onMounted } from "vue";
import axios from "axios";

export default {
  setup() {
    const form = reactive({
      id: null,
      title: "",
      content: "",
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get("http://localhost:3000/articles");
        articles.value = response.data;
      } catch (error) {
        console.error("Error loading articles:", error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/articles/${form.id}`
          : "http://localhost:3000/articles";
        const method = form.id ? "put" : "post";
        const response = await axios[method](url, {
          title: form.title,
          content: form.content,
        });

        if (form.id) {
          const index = articles.value.findIndex(
            (article) => article.id === form.id
          );
          articles.value[index] = response.data;
        } else {
          articles.value.push(response.data);
        }

        form.id = null;
        form.title = "";
        form.content = "";
      } catch (error) {
        console.error("Error saving article:", error);
      }
    }

    async function remove(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter((article) => article.id !== id);
      } catch (error) {
        console.error("Error deleting article:", error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, remove, load };
  },
};
</script>

<style scoped>
body {
  overflow: hidden;
}

.container {
  display: flex;
  flex-direction: column;
  gap: 20px;
  padding: 20px 0px;
  border-radius: 8px;
  max-width: 800px;
  margin: auto;
  height: 100vh;
  overflow: hidden;
}

.form {
  background: #add9e63e;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  gap: 10px;
  backdrop-filter: blur(5px);
}

.input,
.textarea {
  background-color: #ebeef500;
  padding: 10px;
  border: 1px solid #000000;
  border-radius: 4px;
  font-size: 14px;
}

.input:focus,
.textarea:focus {
  background: #a8ebff74;
}

.textarea {
  resize: vertical;
  min-height: 100px;
}

.button {
  padding: 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s ease;
}

.save-button {
  border: 1px solid #000000;
  background-color: #42b98300;
  color: #000000;
}

.save-button:hover {
  color: black;
  background: #a8ebff74;
}

.load-button {
  background-color: #40a0ff00;
  color: #000000;
  align-self: flex-start;
  margin-bottom: 10px;
  margin-left: 120px;
  margin-top: 10px;
  border: 1px solid #000000;
}

.load-button:hover {
  color: black;
  background: #a8ebff74;
}

.articles-section {
  background: #add9e63e;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  height: 300px;
  overflow-y: auto;
  backdrop-filter: blur(5px);
}

.article-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.article-item {
  padding: 10px;
  border-bottom: 1px solid #ebeef5;
  transition: background-color 0.3s ease;
}

.article-content {
  padding: 0 10px;
}

.button-group {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
  margin-top: 10px;
}

.edit-button {
  color: rgb(0, 0, 0);
  padding: 10px 20px;
  background-color: #42b98300;
  margin-right: 100px;
  border: 1px solid #000000;
}

.delete-button {
  padding: 10px 20px;
  background-color: #f56c6c00;
  color: #000000;
  border: 1px solid #000000;
}

.edit-button:hover {
  color: black;
  background: #a8ebff74;
}

.delete-button:hover {
  color: black;
  background: #a8ebff74;
}

input::placeholder,
textarea::placeholder {
  color: rgb(0, 0, 0);
}
</style>
