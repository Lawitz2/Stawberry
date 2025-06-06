# Система логирования Stawberry

## Обзор

Система логирования обеспечивает единый стиль вывода логов для всего приложения, включая внутренние компоненты и HTTP-фреймворк Gin. Основана на высокопроизводительной библиотеке Zap с расширенной функциональностью для улучшения читаемости вывода.

## Ключевые особенности

### Расширенное форматирование

- **Единый формат даты и времени:** `2025-05-20 00:22:41.872`
- **Цветовая схема для улучшения читаемости:**
  - Дата/время и пути к файлам: серый
  - Уровни логирования: цветовая дифференциация (DEBUG: голубой, INFO: зеленый, WARN: желтый, ERROR: красный)
  - HTTP-методы: соответствующие цвета (GET: зеленый, POST: синий, DELETE: красный, и т.д.)
  - Маршруты Gin со стрелками (`→`): упрощение восприятия зарегистрированных обработчиков

### Удобство разработки

- **Скрытие JSON полей** в режиме разработки для повышения читаемости логов
- **Сохранение полных данных** в режиме продакшена для отладки и анализа
- **Выравнивание маршрутов** для более наглядного представления API
- **Умное форматирование** системных и сервисных сообщений

### Технические улучшения

- Интеграция логов Gin в общую систему логирования
- Кастомное форматирование времени и информации о файлах с использованием ANSI-цветов
- Оптимизированный вывод с отключением JSON-полей в режиме разработки
- Гибкая конфигурация в зависимости от окружения

## Архитектура системы логирования

```
Logger System
├── DisabledCore (для режима разработки)
│   ├── Наследует zapcore.Core
│   ├── Отключает вывод JSON-полей
│   └── Сохраняет базовую информацию лога
│
├── Цветные Энкодеры
│   ├── coloredTimeEncoder (для времени)
│   └── coloredCallerEncoder (для источника)
│
└── Middleware (для Gin)
    ├── zapWriter (перенаправление логов Gin)
    ├── ZapLogger (логирование запросов)
    └── ZapRecovery (обработка паник)
```

## Примеры логов

### Логи маршрутов Gin при запуске

```
2025-05-20 00:18:39.508	DEBUG	[GIN] Route: GET /api/sellers/:id/reviews            → GetReviews-fm	component=gin
2025-05-20 00:18:39.508	DEBUG	[GIN] Route: GET /api/auth_required                  → func2	component=gin
2025-05-20 00:18:39.508	DEBUG	[GIN] Route: POST /api/products/:id/reviews           → AddReview-fm	component=gin
```

### Логи сервисов без JSON-полей (в режиме разработки)

```
2025-05-20 00:19:03.054	INFO	reviews/product_reviews.go:68	Existence check
2025-05-20 00:19:03.056	INFO	reviews/product_reviews.go:75	Receiving reviews
2025-05-20 00:19:03.056	INFO	reviews/product_reviews.go:82	Reviews received successfully
```

### Логи HTTP-запросов

```
2025-05-20 00:19:03.057	INFO	GET 200 3.140958ms /api/products/2/reviews
```
