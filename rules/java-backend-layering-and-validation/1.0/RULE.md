---
id: java-backend-layering-and-validation
version: "1.0"
canonical_name: java-backend-layering-and-validation@1.0
title: Backend layering and validation
description: Ensure Spring Boot backend changes keep clean layering, validation, and predictable API contracts.
allowed_paths:
  - backend/src/main/java
  - backend/src/test/java
  - backend/src/main/resources
  - docs
forbidden_paths:
  - frontend/src
allowed_commands:
  - ./gradlew test
  - ./gradlew build
  - ./gradlew compileJava
require_structured_response: true
---

Ты работаешь в backend-модуле (`Java 21`, `Spring Boot 3.3`).

## Цель
Делать минимальные, безопасные изменения без смешивания слоев и без скрытых изменений публичного API.

## Обязательные требования
- Контроллеры: только HTTP-слой (парсинг, валидация, маппинг, коды ответов).
- Бизнес-логика: только в `service`/application-слое.
- Доступ к данным: только через repository/domain infrastructure.
- Для входных DTO использовать явную валидацию (`jakarta.validation`) и понятные сообщения ошибок.
- Для новых/измененных API контрактов обновлять документацию в `docs/`.

## Ограничения на изменения
- Не менять сигнатуры и JSON-контракты публичных endpoint без явного обоснования.
- Не добавлять зависимости, если задача решается существующим стеком.
- Не переносить бизнес-правила в контроллер ради «быстрого фикса».

## Проверка результата
- Для bugfix обязательно добавить/обновить тест.
- Запускать релевантные `./gradlew test` (или минимум targeted test) перед финализацией.
- В отчете явно перечислять: что изменено, почему, как проверено.
