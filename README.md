<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Библиотека 2009: Читать онлайн Дневник памяти и другие книги. Поиск по автору и названию.">
    <title>Библиотека 2009</title>
    <style>
        body { background: #121212; color: #ffffff; font-family: 'Segoe UI', Arial, sans-serif; margin: 0; padding: 20px; text-align: center; }
        .search-container { background: #1e1e1e; padding: 25px; border-radius: 20px; border: 1px solid #333; max-width: 500px; margin: 0 auto 30px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        h1 { color: #007bff; font-size: 28px; margin-bottom: 20px; }
        input { width: 85%; padding: 15px; border-radius: 12px; border: 1px solid #444; background: #252525; color: white; font-size: 16px; margin-bottom: 15px; outline: none; }
        input:focus { border-color: #007bff; }
        button { width: 92%; padding: 15px; background: #007bff; color: white; border: none; border-radius: 12px; font-weight: bold; cursor: pointer; font-size: 16px; transition: 0.3s; }
        button:hover { background: #0056b3; }
        .results-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 20px; max-width: 1200px; margin: auto; }
        .book-card { background: #1e1e1e; padding: 20px; border-radius: 15px; border: 1px solid #333; transition: 0.3s; }
        .book-card:hover { transform: translateY(-5px); border-color: #007bff; }
        img { width: 120px; height: 180px; border-radius: 8px; margin-bottom: 15px; box-shadow: 0 5px 15px rgba(0,0,0,0.4); }
        h3 { font-size: 18px; margin: 10px 0; height: 45px; overflow: hidden; }
        p { color: #aaa; font-size: 14px; margin-bottom: 15px; }
        .read-link { display: inline-block; width: 100%; padding: 12px 0; background: #28a745; color: white; text-decoration: none; border-radius: 10px; font-weight: bold; }
        .read-link:hover { background: #218838; }
    </style>
</head>
<body>

    <div class="search-container">
        <h1>Библиотека 2009</h1>
        <input type="text" id="query" placeholder="Автор или книга (напр. Спаркс)">
        <button onclick="searchBooks()">НАЙТИ И ЧИТАТЬ</button>
    </div>

    <div id="results" class="results-grid"></div>

    <script>
        async function searchBooks() {
            const val = document.getElementById('query').value;
            const resBox = document.getElementById('results');
            if(!val) return;

            resBox.innerHTML = '⚡ Подключаюсь к архиву...';

            try {
                // Улучшенный запрос: ищет и по автору, и по названию одновременно
                const response = await fetch(`https://www.googleapis.com/books/v1/volumes?q=${encodeURIComponent(val)}&langRestrict=ru&orderBy=relevance&maxResults=12`);
                const data = await response.json();
                resBox.innerHTML = '';

                if(!data.items) {
                    resBox.innerHTML = '❌ Книга не найдена. Попробуйте ввести фамилию автора.';
                    return;
                }

                data.items.forEach(book => {
                    const info = book.volumeInfo;
                    const cover = info.imageLinks ? info.imageLinks.thumbnail : 'https://via.placeholder.com/120x180?text=Нет+обложки';
                    const author = info.authors ? info.authors.join(', ') : 'Автор не указан';
                    // Используем webReaderLink для профессионального режима чтения
                    const reader = book.accessInfo.webReaderLink || info.previewLink;

                    resBox.innerHTML += `
                        <div class="book-card">
                            <img src="${cover}" alt="обложка">
                            <h3>${info.title}</h3>
                            <p>👤 ${author}</p>
                            <a href="${reader}" target="_blank" class="read-link">ЧИТАТЬ ОНЛАЙН</a>
                        </div>`;
                });
            } catch (e) {
                resBox.innerHTML = '⚠️ Ошибка поиска. Проверьте интернет.';
            }
        }
    </script>
</body>
</html>
