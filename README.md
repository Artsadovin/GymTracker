# GymTracker

PWA-приложение для логирования силовых тренировок. Работает полностью офлайн, данные хранятся в IndexedDB.

## Особенности

- 📱 **PWA** — устанавливается на главный экран iPhone/Android как нативное приложение
- 💪 **Быстрый ввод** — запись подхода за 2-3 тапа большими кнопками
- ⏱ **Таймер отдыха** — с вибрацией и звуком
- 📊 **Прогресс** — графики 1RM (формула Эпли), макс. вес, объём, личные рекорды
- 📅 **Недельный план** — фиксированное расписание по дням недели
- 📜 **История** — просмотр, редактирование и удаление прошлых тренировок
- 💾 **Экспорт/Импорт** — JSON бэкап одной кнопкой
- 🌙 **Тёмная тема** — минималистичный дизайн, крупные шрифты, mobile-first

## Стек

- Vite + React + TypeScript
- Tailwind CSS
- Recharts (графики)
- Dexie.js (IndexedDB)
- vite-plugin-pwa (Service Worker + Web Manifest)

## Запуск локально

```bash
# Установка зависимостей
npm install

# Режим разработки
npm run dev

# Продакшн-сборка
npm run build

# Предпросмотр продакшн-сборки
npm run preview
```

## Деплой

### GitHub Pages

1. В настройках репозитория включите **GitHub Pages** → Source: **GitHub Actions**
2. Создайте файл `.github/workflows/deploy.yml`:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/deploy-pages@v4
        id: deployment
```

3. В `vite.config.ts` установите `base: '/<имя-репозитория>/'` для деплоя в подпапку.

### Cloudflare Pages

1. Подключите репозиторий в Cloudflare Pages
2. Build command: `npm run build`
3. Build output directory: `dist`
4. Добавьте `_headers` в `public/` для кэширования:

```
/sw.js
  Cache-Control: no-cache
/manifest.webmanifest
  Cache-Control: public, max-age=31536000
/*.png
  Cache-Control: public, max-age=31536000
/*.svg
  Cache-Control: public, max-age=31536000
```

## Установка на iPhone (Safari)

1. Откройте приложение в Safari
2. Нажмите **Поделиться** (квадрат со стрелкой вверх)
3. Пролистайте вниз и выберите **«На экран "Домой"»**
4. Нажмите **Добавить**
5. Приложение откроется в полноэкранном режиме без адресной строки

## Установка на Android (Chrome)

1. Откройте приложение в Chrome
2. Меню (три точки) → **«Установить приложение»** или **«Добавить на главный экран»**
3. Подтвердите установку

## Структура проекта

```
src/
├── components/     # UI компоненты (TabBar, Modal, Toast, ExerciseCard, ConfirmDialog)
├── db/             # Dexie база данных
├── screens/        # Экраны (Today, Progress, History, Plan, Settings)
├── types/          # TypeScript типы и утилиты
├── styles/         # Глобальные стили (Tailwind)
├── App.tsx         # Главный компонент
└── main.tsx        # Точка входа
```

## Модель данных

- **WorkoutPlan** — план недели (дни → упражнения)
- **WorkoutSession** — одна тренировка с упражнениями и подходами
- **SetEntry** — один подход (вес × повторы)
- **AppSettings** — настройки (таймер, шаги, вибрация, звук, единицы)

## Формула Эпли для 1RM

```
1RM = weight × (1 + reps / 30)
```

## Лицензия

MIT — для личного использования.