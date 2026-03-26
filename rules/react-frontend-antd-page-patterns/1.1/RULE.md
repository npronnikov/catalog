---
id: react-frontend-antd-page-patterns
version: "1.0"
canonical_name: react-frontend-antd-page-patterns@1.0
title: Frontend React page patterns
description: Keep frontend changes aligned with existing React + Ant Design patterns and predictable UX.
allowed_paths:
  - frontend/src
  - frontend/public
  - docs
forbidden_paths:
  - backend/src
allowed_commands:
  - npm --prefix frontend run build
  - npm --prefix frontend run dev
require_structured_response: true
---

Ты работаешь в frontend-модуле (`React 18`, `Vite`, `Ant Design`). Весрия 1.1

## Цель
Поддерживать единый UX-паттерн редакторов (`Rules`, `Skills`, `Flows`, `Versions`) без визуальной и архитектурной деградации.

## Обязательные требования
- Переиспользовать существующие компоненты из `frontend/src/components` и утилиты из `frontend/src/utils`.
- Следовать текущей структуре страниц из `frontend/src/pages`.
- Сохранять консистентность состояния загрузки/ошибок/пустых состояний.
- Любой новый API-вызов делать через общий слой `frontend/src/api/request.js`.
- Не ломать существующую маршрутизацию (`react-router-dom`) и навигацию в `AppShell`.

## Ограничения
- Не вносить изменения в backend-контракты из frontend-задач.
- Не добавлять тяжелые UI-зависимости при наличии компонента в `antd`.
- Не дублировать логику между страницами, если ее можно вынести в утилиту/компонент.

## Проверка результата
- Проверять сборку `npm --prefix frontend run build`.
- Для изменений UX перечислять сценарии ручной проверки (happy path + error path).
- В отчете указывать затронутые страницы и компоненты.
