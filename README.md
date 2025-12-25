<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <title>Мини-соцсеть</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
    }
    body {
      background-color: #f0f2f5;
      color: #1c1e21;
      line-height: 1.5;
      padding: 10px;
    }
    .container {
      max-width: 600px;
      margin: 0 auto;
    }
    .header {
      text-align: center;
      margin-bottom: 20px;
      color: #1877f2;
      font-size: 24px;
      font-weight: bold;
    }
    .compose {
      display: flex;
      gap: 10px;
      margin-bottom: 20px;
      background: white;
      border-radius: 8px;
      padding: 12px;
      box-shadow: 0 1px 2px rgba(0,0,0,0.1);
    }
    #message {
      flex-grow: 1;
      border: none;
      outline: none;
      resize: none;
      height: 40px;
      padding: 8px;
      border-radius: 6px;
      background-color: #f8f9fa;
      font-size: 14px;
    }
    button {
      background-color: #1877f2;
      color: white;
      border: none;
      border-radius: 6px;
      padding: 0 16px;
      cursor: pointer;
      font-weight: 600;
      font-size: 14px;
    }
    button:hover {
      background-color: #166fe5;
    }
    .feed {
      background: white;
      border-radius: 8px;
      box-shadow: 0 1px 2px rgba(0,0,0,0.1);
      overflow: hidden;
    }
    .post {
      padding: 16px;
      border-bottom: 1px solid #e4e6ea;
      animation: fadeIn 0.3s ease-out;
    }
    .post:last-child {
      border-bottom: none;
    }
    .post-content {
      white-space: pre-wrap;
      word-break: break-word;
      font-size: 15px;
      color: #1c1e21;
    }
    .post-time {
      font-size: 12px;
      color: #65676b;
      margin-top: 6px;
    }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(-10px); }
      to { opacity: 1; transform: translateY(0); }
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">Моя мини-соцсеть</div>
    <div class="compose">
      <textarea id="message" placeholder="Напишите что-нибудь..."></textarea>
      <button id="send">Отправить</button>
    </div>
    <div class="feed" id="feed"></div>
  </div>

  <script>
    // Ключи для хранения в localStorage
    const STORAGE_KEY = 'miniSocialPosts';

    // Получаем посты из localStorage
    function getPosts() {
      const stored = localStorage.getItem(STORAGE_KEY);
      return stored ? JSON.parse(stored) : [];
    }

    // Сохраняем посты в localStorage
    function savePosts(posts) {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(posts));
    }

    // Добавляем новый пост
    function addPost(text) {
      if (!text || text.trim().length === 0) return;

      const posts = getPosts();
      const newPost = {
        id: Date.now() + Math.random(), // уникальный ID
        text: text.trim(),
        time: new Date().toISOString()
      };
      posts.unshift(newPost); // новый пост вверху
      savePosts(posts);
      renderPosts(); // перерисовываем ленту
    }

    // Форматируем дату как "10:30" или "вчера 10:30"
    function formatTime(isoString) {
      const date = new Date(isoString);
      const now = new Date();
      const diff = now - date;
      const oneDay = 24 * 60 * 60 * 1000;

      if (diff < oneDay) {
        return date.toLocaleTimeString('ru', {
          hour: '2-digit',
          minute: '2-digit'
        });
      } else {
        return date.toLocaleDateString('ru') + ' ' +
               date.toLocaleTimeString('ru', {
                 hour: '2-digit',
                 minute: '2-digit'
               });
      }
    }

    // Рисуем ленту постов
    function renderPosts() {
      const posts = getPosts();
      const feed = document.getElementById('feed');
      let html = '';

      posts.forEach(post => {
        html += `
          <div class="post">
            <div class="post-content">${post.text}</div>
            <div class="post-time">${formatTime(post.time)}</div>
          </div>
        `;
      });

      feed.innerHTML = html;
    }

    // Инициализация
    document.addEventListener('DOMContentLoaded', () => {
      // Обработчик отправки
      document.getElementById('send').addEventListener('click', () => {
        const input = document.getElementById('message');
        addPost(input.value);
        input.value = ''; // очищаем поле
        input.focus();
      });

      // Обработка Enter
      document.getElementById('message').addEventListener('keydown', (e) => {
        if (e.key === 'Enter' && !e.shiftKey) {
          e.preventDefault();
          document.getElementById('send').click();
        }
      });

      // Первоначальное отображение
      renderPosts();

      // Автообновление каждые 1.3 секунды
      setInterval(renderPosts, 1300);
    });
  </script>
</body>
</html>
