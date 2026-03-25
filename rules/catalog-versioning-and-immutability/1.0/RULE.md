---
id: catalog-versioning-and-immutability
version: "1.0"
canonical_name: catalog-versioning-and-immutability@1.0
title: Catalog versioning and immutability
description: Enforce immutable published versions and strict catalog repository layout for rules/skills/flows.
allowed_paths:
  - docs
  - backend/src/main/java
  - backend/src/main/resources
  - backend/src/test
  - git
forbidden_paths:
  - frontend/src
allowed_commands:
  - ./gradlew test
  - ./gradlew build
require_structured_response: true
---

Это правило для задач публикации и синхронизации каталога (`rules/skills/flows`).

## Цель
Гарантировать воспроизводимую публикацию: новая версия создается добавлением новой директории, а опубликованный контент не редактируется «на месте».

## Обязательные требования
- Структура каталога фиксирована: `<entity>/<id>/<version>/{RULE.md|SKILL.md|FLOW.yaml, metadata.yaml}`.
- `canonical_name` всегда равен `id@version`.
- `metadata.yaml` должен содержать валидный `checksum` контент-файла (sha256).
- Для опубликованной версии разрешено только создание новой версии, а не изменение старой.
- Пути в `source_path` должны быть относительными внутри каталога.

## Ограничения
- Не нарушать совместимость с импортом через `SettingsService`.
- Не ослаблять проверки целостности ради обхода ошибок публикации.

## Проверка результата
- Проверить, что структура и поля `metadata.yaml` соответствуют ожидаемому формату.
- Проверить, что checksum совпадает с фактическим содержимым.
- В отчете фиксировать, какие сущности и версии были добавлены.
