# battery_type — каталог типов аккумуляторов

Итоговая ветка `battery_type-lab8`: React, TypeScript, Redux Toolkit, Axios, Bootstrap, Three.js, CLIP и PWA.
Исходная ЛР5 без адаптивности и без API сохранена в `battery_type-lab5`.

## Запуск

`npm ci`, затем `npm run dev`. Открыть http://127.0.0.1:53001/#/battery_type . Vite проксирует `/api` на NestJS :53000.
`npm run build` проверяет TypeScript и создаёт dist. Для mock: установить `VITE_BATTERY_TYPE_MOCK=true` перед сборкой.

## Страницы и действия

Плитка: фильтр ёмкости, пагинация по 24 типа, обновление данных и CLIP-поиск. Лента: модель 3D, атрибуты, лайки и следующий элемент набора Redis. Добавление: один черновик, изображение и GLB/GLTF, публикация ёмкости и напряжения в EAV. Вход: регистрация, Redis-сессия, выход.

Redux хранит фильтр, страницу и пользователя; после F5 пользователь в Redux — гость, cookie сессии может сохраняться. Пароли в клиентское хранилище не записываются. В thunk загрузки используется Axios-клиент из Swagger (`src/modules/generated/BatteryTypeApi.ts`).

## Дополнительные задания

Пагинация получает `items,total,page,limit` от API. Лента Redis — отдельный набор из 100 уникальных id на гостевую/пользовательскую сессию.
`?next=true` запускает asyncio-задачу. Polling раз в секунду использует постоянный id и `next=false`, поэтому не создаёт новые задачи.

CLIP (`Xenova/clip-vit-base-patch32`, Transformers.js) работает в Web Worker. Веса скачиваются при первом запуске. Изображение не отправляется API. Английские описания берутся из `/api/battery_type/descriptions`; одинаковые подписи индексируются один раз, показывается представитель группы. Метрика S=(1+cosine)/2 в пределах 0–1. Начальные порог 0.63 и TopK 3 выбраны на пробном изображении (0.639, 0.632, 0.625); это не статистическая оценка точности. В интерфейсе порог 0.4–0.9 и TopK 1–20.

## Репозитории

- Бэкенд: https://github.com/NXC0BM/Battery-type/tree/battery_type-lab8
- Mock Pages: https://nxc0bm.github.io/Battery-type-frontend/
- Tauri: https://github.com/NXC0BM/Battery-type-native
- Asyncio: https://github.com/NXC0BM/Battery-type-async


Дополнение преподавателя от 25.11: CLIP сравнивает картинку с английским полем БД short_description_en только у карточек текущей страницы пагинации. Текстовые эмбеддинги кэшируются в Redux; изображение вычисляется при каждом поиске. Проверка: tools/verify_clip_redux.cjs в основном репозитории.
