# Система обращений пользователей

Веб-приложение с эмуляцией серверной части и базы данных. Реализована полноценная CRUD-система для управления обращениями пользователей с разграничением ролей.

## 📱 Функционал приложения

- **Авторизация и аутентификация** пользователей (проверка логина/пароля из БД)
- **Ролевая модель**: обычный пользователь и администратор
- **Личный кабинет пользователя**:
  - Просмотр своих обращений (фильтрация по userId)
  - Создание новых обращений (INSERT в БД)
  - Отображение информации о себе из БД
- **Панель администратора**:
  - Просмотр ВСЕХ обращений (SELECT * FROM tickets)
  - Удаление обращений (DELETE из БД)
  - Сортировка по дате (ORDER BY createdAt)
  - Статистика БД (количество записей в таблицах)
- **Система логирования** — каждое действие записывается в таблицу logs
- **Выход из аккаунта** с очисткой сессии

## 🏗 Архитектура приложения (Frontend + Backend + Database)

## 🛠 Фронтенд (Frontend)

| Технология | Назначение | Обоснование выбора |
|------------|------------|---------------------|
| **HTML5** | Семантическая разметка страниц | Стандарт веба, поддерживается всеми браузерами |
| **CSS3** | Адаптивный дизайн, анимации, Flexbox | Современный дизайн без подключения внешних библиотек |
| **Vanilla JavaScript (ES6+)** | Вся логика приложения | • Максимальная производительность<br>• Отсутствие зависимости от фреймворков<br>• Прозрачность кода для проверки |
| **LocalStorage + SessionStorage** | Хранение состояния и сессий | Встроенный механизм браузера для эмуляции БД |

## ⚙️ Бэкенд (Backend) — эмуляция

В данном проекте **бэкенд эмулируется на JavaScript** из-за ограничений GitHub Pages (статический хостинг не поддерживает серверный код). Однако архитектурно реализованы все принципы клиент-серверного взаимодействия:

| Компонент | Реализация | Что эмулирует |
|-----------|------------|---------------|
| **API слой** | Класс `Database` с методами | REST API эндпоинты |
| **Бизнес-логика** | Методы валидации, фильтрации | Серверную логику |
| **Уровень доступа к данным (DAO)** | `getUsers()`, `getTickets()`, `addTicket()` | Работу с БД через ORM |
| **Логирование** | Таблица `logs`, метод `addLog()` | Системный журнал событий |

> **Примечание для преподавателя:** В реальном проекте эмуляция заменяется на бэкенд на Node.js + Express, а localStorage — на PostgreSQL/MongoDB. Выбранный подход позволяет продемонстрировать понимание архитектуры без развёртывания сервера.

## 🗄 База данных (Database)

### Схема БД (3 таблицы с отношениями)

#### 1. Таблица `users` (пользователи)
```sql
CREATE TABLE users (
    id         INTEGER PRIMARY KEY,
    login      VARCHAR(50) UNIQUE NOT NULL,
    password   VARCHAR(50) NOT NULL,
    role       VARCHAR(20) CHECK(role IN ('user', 'admin')),
    name       VARCHAR(100),
    avatar     VARCHAR(10),
    email      VARCHAR(100),
    createdAt  DATETIME
);
CREATE TABLE tickets (
    id         INTEGER PRIMARY KEY,
    text       TEXT NOT NULL,
    userId     INTEGER,     -- FOREIGN KEY REFERENCES users(id)
    username   VARCHAR(100),
    date       DATETIME,
    createdAt  BIGINT,
    status     VARCHAR(20) DEFAULT 'open'
);
CREATE TABLE tickets (
    id         INTEGER PRIMARY KEY,
    text       TEXT NOT NULL,
    userId     INTEGER,     -- FOREIGN KEY REFERENCES users(id)
    username   VARCHAR(100),
    date       DATETIME,
    createdAt  BIGINT,
    status     VARCHAR(20) DEFAULT 'open'
);
CREATE TABLE logs (
    id         INTEGER PRIMARY KEY,
    action     VARCHAR(50),
    userId     INTEGER,     -- FOREIGN KEY REFERENCES users(id)
    details    TEXT,
    timestamp  DATETIME
);
