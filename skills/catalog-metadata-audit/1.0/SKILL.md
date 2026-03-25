---
name: Catalog metadata audit
description: Validate catalog structure and metadata integrity for rules/skills/flows. Use before publish/sync and after catalog changes.
---

# Catalog metadata audit

## Goal
Проверить, что каталог готов к безопасной синхронизации и индексации.

## Workflow
1. Проверь структуру: `<entity>/<id>/<version>/{content, metadata.yaml}`.
2. Проверь обязательные поля `metadata.yaml`: `entity_type`, `id`, `version`, `canonical_name`, `checksum`, `coding_agent`.
3. Проверь правило `canonical_name == id@version`.
4. Проверь соответствие контент-файла типу:
- `rule` -> `RULE.md`
- `skill` -> `SKILL.md`
- `flow` -> `FLOW.yaml`
5. Пересчитай sha256 контента и сравни с `checksum`.
6. Убедись, что `source_path` относительный и указывает на папку версии.

## Output requirements
- Верни список ошибок по файлам и полям.
- Для каждой ошибки предложи точечное исправление.
- Если ошибок нет, верни краткий отчет `Catalog audit: OK`.
