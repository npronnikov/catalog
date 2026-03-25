---
name: Restore C4 architecture
description: Reconstruct C4 architecture view from the codebase. Use when documentation is outdated or missing.
---

# Restore C4 architecture

## Goal
Восстановить актуальную C4-картину системы по исходному коду и документации.

## Workflow
1. Просмотри `README.md`, `docs/architecture`, `docs/adr`, `backend`, `frontend`, `infra`.
2. Определи уровень Context: акторы, внешние системы, границы продукта.
3. Определи уровень Container: backend, frontend, хранилища, интеграции, протоколы.
4. Определи уровень Component: ключевые модули внутри контейнеров и их ответственности.
5. Зафиксируй вывод в `docs/c-4.md` с четкими секциями Context/Container/Component.

## Output requirements
- Не придумывай несуществующие сервисы или интеграции.
- Указывай только подтвержденные связи и ответственность.
- Явно отмечай предположения как `Assumption`.
