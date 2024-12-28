# Инструкция по работе с дашбордом в Superset

## Шаги для работы

### 1. Авторизация
Перейдите по ссылке и авторизуйтесь в Superset: [http://91.185.86.67:8080/](http://91.185.86.67:8080/).

### 2. Проверка подключения к базе данных
1. Переходим в раздел **Settings -> Database Connections**.
2. Проверка наличие подключения к Greenplum с названием `IDP_GreenPlum` на хосте `91.185.85.179`.

### 3. Построение чартов и дашбордов
1. Работа с данными начинается в **SQL Lab**.
2. Основные этапы:
   - **Создание DataSet.**
   - **Создание Chart.**
   - **Создание Dashboard.**

## Примеры

### 1. Топ-10 фильмов по рейтингу через кастомный DataSet
1. Выполняем следующий SQL-запрос в SQL Lab и сохраняем его как DataSet с названием `team_5_top_10_movies`:

```sql
WITH top_rn AS (
  SELECT title,
         user_score,
         genres,
         release_date,
         runtime_hour,
         row_number() OVER (
           ORDER BY user_score DESC
         ) rn
  FROM team_5_movies_internal
)
SELECT title,
       user_score,
       genres,
       release_date,
       runtime_hour
FROM top_rn
WHERE rn < 11;
```

2. Используем созданный DataSet `team_5_chart_top_10_movies_by_score` для создания чарта типа **Table**. В чарт добавляем все колонки.

### 2. Метрики на основе таблицы
#### a) Общее время для просмотра всех фильмов (в часах)
- Тип чарта: **Big Number**.
- Метрика: `sum(runtime_hour)`.

#### b) Общее количество фильмов
- Тип чарта: **Big Number**.
- Метрика: `count(id)`.

#### c) Распределение количества фильмов по дате выпуска
- Тип чарта: **Line Chart**.
- Time Grain: `YEAR`.
- Метрика: `count(id)`.

#### d) Распределение фильмов по языку
- Тип чарта: **Bar Chart**.
- X-Axis: `language`.
- Метрика: `count(id)`.
- Фильтр: `count(title) > 50` (для исключения шума).

#### e) Распределение фильмов по жанрам
- Тип чарта: **Pie Chart**.
- Dimensions: `genres`.
- Метрика: `count(*)`.
- Фильтр: `count(*) > 50` (для исключения шума).
