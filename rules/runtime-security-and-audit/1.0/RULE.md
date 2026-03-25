---
id: runtime-security-and-audit
version: "1.0"
canonical_name: runtime-security-and-audit@1.0
title: Runtime security and audit discipline
description: Preserve security boundaries, secret handling, and full auditability of runtime operations.
allowed_paths:
  - backend/src/main/java
  - backend/src/main/resources
  - docs
forbidden_paths:
  - frontend/src
allowed_commands:
  - ./gradlew test
  - ./gradlew build
require_structured_response: true
---

Это правило применимо к задачам, где затрагиваются runtime, execution, gate approval, secrets и аудит.

## Цель
Сохранить наблюдаемость и безопасность рантайма без упрощений, которые скрывают риски.

## Обязательные требования
- Секреты не должны логироваться или возвращаться в API-ответах.
- Для чувствительных операций обязателен явный audit trail (`кто`, `когда`, `что изменил`).
- Ошибки должны быть диагностируемыми, но без утечки приватных данных.
- Валидация входных параметров обязательна на границе API.
- Изменения, влияющие на безопасность, должны быть отражены в документации/ADR.

## Ограничения
- Не отключать проверки авторизации/валидации ради прохождения сценария.
- Не ослаблять ограничения путей/команд в runtime-правилах без отдельного обоснования.

## Проверка результата
- Покрыть критичные ветки тестами (happy path + forbidden/invalid path).
- В отчете отдельно описывать влияние на security и audit.
