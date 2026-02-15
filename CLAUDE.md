# Bunny Accountant — CLAUDE.md

## Що це

PWA-додаток для обліку продажу кролятини. Мова інтерфейсу — українська. Валюта — гривня (₴).

## Архітектура

**Монолітний single-file додаток** — весь React-код, стилі та логіка в одному файлі `rabbit-sales-app.html` (~2900 рядків).

### Структура файлів

```
rabbit-sales-app.html   — Головний файл (React 18, JSX через Babel CDN)
service-worker.js       — PWA кешування (network-first для HTML, cache-first для статики)
manifest.json           — PWA метадані
icon-192.png / icon-512.png — Іконки
```

### Компоненти (всередині HTML)

```
App (~рядок 1383)         — Головний компонент, стейт, роутинг
├── Dashboard             — Аналітика, список продажів, Chart.js графіки
├── Customers             — Управління клієнтами
├── Settings              — Налаштування ціни, Google Sheets підключення
├── SaleModal             — Додавання/редагування продажу
├── CustomerModal         — Додавання/редагування клієнта
└── BottomNav             — Мобільна навігація (3 вкладки)
```

## Стек

- **React 18** + **ReactDOM 18** (CDN, unpkg)
- **Babel Standalone** (JSX компіляція в браузері)
- **Chart.js 4.4.0** (графіки)
- **date-fns 3.0.0** (робота з датами)
- **Google Sheets API v4** + **OAuth 2.0** (опціональна синхронізація)
- **Без package.json, без збірки, без Node.js**

## Зберігання даних

Все в `localStorage`:
- `rabbitSales` — масив продажів (JSON)
- `rabbitCustomers` — масив клієнтів (JSON)
- `rabbitSettings` — налаштування (JSON)
- `googleApiConfig` — API ключі Google (JSON)

## Моделі даних

**Sale**: `{ id, date (YYYY-MM-DD), weight, pricePerKg, totalAmount, customerId, status: 'active'|'done' }`

**Customer**: `{ id, name, phone }`

**Settings**: `{ defaultPricePerKg, spreadsheetId, spreadsheetName, isConnected }`

## Синхронізація з Google Sheets

- Опціональна (працює і офлайн)
- OAuth 2.0 через popup
- Дебаунс 2 сек при змінах + автосинк кожні 60 сек
- Двостороння: імпорт/експорт
- Аркуші: "Sales", "Customers"
- Custom events: `salesImported`, `customersImported`

## Як запустити

```bash
# Локальний сервер (для PWA і OAuth)
python -m http.server 8000
# або
npx http-server -p 8000
# Відкрити http://localhost:8000/rabbit-sales-app.html
```

## Правила роботи з кодом

- **Один файл** — всі зміни в `rabbit-sales-app.html`
- **Без збірки** — зміни видно одразу після рефрешу
- **Без тестів** — тестування мануальне
- **Мова UI** — українська, не перекладати на англійську
- **Стилі** — inline в `<style>` тегу всередині HTML
- **Emoji в UI** — активно використовуються
