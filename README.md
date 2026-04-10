# Лабораторная работа №28: Веб-сервер на C# — список любимых игр с полным управлением

## Описание проекта

Данный проект представляет собой полноценное REST API для управления списком любимых видеоигр. Сервер реализует полный набор CRUD-операций (Create, Read, Update, Delete) с использованием ASP.NET Core Web API. Данные хранятся в памяти сервера (статический список), что позволяет продемонстрировать принципы работы контроллеров, маршрутизации и различных HTTP-методов (GET, POST, PUT, DELETE).

Проект создан в рамках лабораторной работы №28 для изучения:
- Создания веб-сервера на ASP.NET Core с шаблоном `webapi`;
- Использования контроллеров вместо Minimal API;
- Обработки HTTP-запросов с правильными статус-кодами (200, 201, 204, 404, 400);
- Тестирования API через инструмент командной строки `curl`.

## Инструкция по запуску

Для запуска сервера выполните одну команду в терминале из корневой директории проекта `Lab28_GamesList`:

```bash
cd GamesApi && dotnet run
```

После запуска в терминале появится сообщение с адресом сервера, например:
```
Now listening on: http://localhost:5074
```
Откройте браузер или используйте `curl` для отправки запросов на этот адрес (подставьте ваш порт).

## Таблица всех маршрутов

| Метод | Маршрут | Описание | Статус |
| :--- | :--- | :--- | :--- |
| GET | `/api/games` | Получить все игры | 200 OK |
| GET | `/api/games/{id}` | Получить игру по идентификатору | 200 OK / 404 Not Found |
| POST | `/api/games` | Добавить новую игру | 201 Created / 400 Bad Request |
| PUT | `/api/games/{id}` | Обновить существующую игру | 200 OK / 404 Not Found |
| DELETE | `/api/games/{id}` | Удалить игру по идентификатору | 204 No Content / 404 Not Found |

## Примеры curl-команд для каждого маршрута

> **Примечание:** 
### 1. GET /api/games — получить все игры
```bash
curl http://localhost:5074/api/games
```

### 2. GET /api/games/{id} — получить игру по id
```bash
curl http://localhost:5074/api/games/1
```

### 3. POST /api/games — добавить новую игру
```bash
curl -X POST http://localhost:5074/api/games \
  -H "Content-Type: application/json" \
  -d "{\"title\": \"Stardew Valley\", \"genre\": \"Simulation\", \"releaseYear\": 2016}"
```

### 4. PUT /api/games/{id} — обновить игру
```bash
curl -X PUT http://localhost:5074/api/games/2 \
  -H "Content-Type: application/json" \
  -d "{\"title\": \"The Witcher 3\", \"genre\": \"RPG\", \"releaseYear\": 2015}"
```

### 5. DELETE /api/games/{id} — удалить игру
```bash
curl -X DELETE http://localhost:5074/api/games/1
```

### 6. Просмотр статус-кода и заголовков (флаг -i)
```bash
curl -i http://localhost:5074/api/games/99
```

## Дополнительные задания (выполненные)

### Задание 1. Поле IsFavourite и маршрут /favourites
- В модель `Game` добавлено поле `IsFavourite` (по умолчанию `false`).
- Добавлен маршрут `GET /api/games/favourites`, возвращающий только избранные игры.

**Пример curl:**
```bash

curl -X POST http://localhost:5074/api/games \
  -H "Content-Type: application/json" \
  -d "{\"title\": \"Portal 2\", \"genre\": \"Puzzle\", \"releaseYear\": 2011, \"isFavourite\": true}"


curl http://localhost:5074/api/games/favourites
```

### Задание 2. Защита от пустого названия
В метод `Create` добавлена проверка: если `game.Title` пустой или состоит из пробелов — возвращается статус `400 Bad Request` с сообщением об ошибке.

**Пример curl (должен вернуть 400):**
```bash
curl -X POST http://localhost:5074/api/games \
  -H "Content-Type: application/json" \
  -d "{\"title\": \"\", \"genre\": \"RPG\", \"releaseYear\": 2020}"
```

## Структура проекта

```
Lab28_GamesList/
├── GamesApi/                      # Корень веб-приложения
│   ├── Controllers/
│   │   └── GamesController.cs     # Контроллер с маршрутами API
│   ├── Data/
│   │   └── GamesStore.cs          # Статическое хранилище (список игр)
│   ├── Models/
│   │   └── Game.cs                # Модель данных "Игра"
│   ├── Properties/
│   │   └── launchSettings.json    # Настройки запуска (порты, окружение)
│   ├── Program.cs                 # Точка входа (регистрация контроллеров)
│   ├── GamesApi.csproj            # Файл проекта
│   └── appsettings.json           # Конфигурация приложения
├── img/                           # Скриншоты выполнения работы
└── README.md                      # Описание проекта
```

## Итоговая таблица ключевых концепций

| Концепция | Описание |
| :--- | :--- |
| `public static class` | Класс существует в одном экземпляре, данные общие для всего приложения |
| `List<Game>` | Список объектов в памяти — простое хранилище без базы данных |
| `FirstOrDefault(...)` | Найти первый элемент по условию, вернуть `null` если не найден |
| `[HttpPost]` | Маршрут принимает POST-запросы |
| `[HttpDelete("{id}")]` | Маршрут принимает DELETE-запросы с параметром в URL |
| `[FromBody]` | Данные берутся из тела запроса (JSON) |
| `Ok(...)` | Вернуть статус 200 с данными |
| `CreatedAtAction(...)` | Вернуть статус 201 с адресом нового объекта |
| `NotFound(...)` | Вернуть статус 404 с сообщением |
| `NoContent()` | Вернуть статус 204 — успех без тела ответа |
| `curl` | Инструмент командной строки для отправки HTTP-запросов |
| `curl -X POST` | Указать метод запроса |
| `curl -H "..."` | Добавить заголовок к запросу |
| `curl -d "..."` | Тело запроса |
| `curl -i` | Показать статусный код и заголовки ответа |
```
