---
name: Restore sequence diagrams
description: Reconstruct sequence diagrams for integration and runtime flows. Use when interaction logic must be documented from code.
---

# Restore sequence diagrams new

## Goal
Восстановить последовательности взаимодействий между компонентами и внешними системами.

## Workflow
1. Определи ключевые сценарии (happy path + error path).
2. Для каждого сценария зафиксируй участников и сообщения в порядке времени.
3. Отрази ветвления, ошибки, retry/timeout, если они есть в коде.
4. Сверь диаграммы с фактическими endpoint/service/repository вызовами.
5. Сохрани результат в `docs/sequence.md`.

## Output requirements
- Используй UML/Mermaid-совместимое представление.
- Не пропускай критичные шаги валидации и авторизации.
- Для каждого сценария добавь короткое описание триггера и результата.
