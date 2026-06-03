# JIRA Questions Gate Flow

Работа с уточняющими вопросами и JIRA задачами через объединённый flow с поддержкой MCP сервера.

## Описание

Flow позволяет:
1. Сгенерировать уточняющие вопросы в формате human-form
2. Создать JIRA задачу с assignee на конкретного человека через MCP сервер Atlassian
3. Указать project (например: PROJECT) и контур (SIGMA/DELTA/SBERTRACK)
4. Пользователь заполняет вопросы в gate UI
5. Результаты связываются с JIRA задачей

## Использование

Запустить flow с параметрами:
- `env.feature_request` — описание запроса (обязательно)
- `env.assign` — email или identifier человека, которому назначается задача (обязательно)
- `env.project` — JIRA project key (обязательно, например: PROJECT)
- `env.environment` — контур: SIGMA, DELTA или SBERTRACK (обязательно)

### Примеры запросов

```
env.feature_request: "Нужно добавить функцию экспорта отчётов в PDF"
env.assign: "user@example.com"
env.project: "PROJECT"
env.environment: "SIGMA"
```

```
env.feature_request: "Реализовать авторизацию через SSO"
env.assign: "ivanov@example.com"
env.project: "PROJECT"
env.environment: "DELTA"
```

## Файлы

- `1.0/FLOW.yaml` — описание flow
- `1.0/metadata.yaml` — метаданные flow
- `1.0/README.md` — этот файл
- `questions.form.json` — уточняющие вопросы (выходной artifact)
- `jira-question-result.md` — результат работы с JIRA (выходной artifact)

## Скилл

Использует скилл `jira-issues@1.0` из директории `catalog/skills/jira-issues/1.0/`

## Структура output

### questions.form.json

```json
{
  "kind": "human-form",
  "version": "1",
  "title": "Уточняющие вопросы",
  "fields": [
    {
      "id": "export_format",
      "type": "radio",
      "label": "В каком формате нужен экспорт?",
      "required": true,
      "options": ["PDF", "Excel", "CSV", "Word"],
      "value": null
    },
    {
      "id": "report_details",
      "type": "textarea",
      "label": "Дополнительные детали для отчёта:",
      "required": false,
      "value": null
    }
  ]
}
```

## Участники

- **Product Owner** — заполняет форму с вопросами в gate UI
- **Jira user** (assignee) — человек, назначенный на задачу

## Структура flow

1. **predefined-questions** — генерация уточняющих вопросов
2. **create-jira-task** — создание JIRA задачи с вопросами (MCP сервер)
3. **human-fill-form** — заполнение формы пользователем
4. **update-jira-task** — обновление JIRA задачи с ответами (MCP сервер)
5. **terminal-complete** — завершение

## MCP инструменты и выбор по контуру

Используются следующие MCP инструменты Atlassian Jira:

| Контур | MCP сервер | Создание задачи | Получение задачи | Комментарий |
|--------|------------|-----------------|------------------|-------------|
| **SIGMA** | Sigma Atlassian | `jira_create_issue` | `jira_get_issue` | `jira_add_comment` |
| **DELTA** | Delta Atlassian | `jira_create_issue` | `jira_get_issue` | `jira_add_comment` |
| **SBERTRACK** | SberTrack | `jira_create_issue` | `jira_get_issue` | `jira_add_comment` (позже) |

Выбор MCP сервера осуществляется автоматически по значению `env.environment`:
- `SIGMA` → Sigma Atlassian
- `DELTA` → Delta Atlassian
- `SBERTRACK` → SberTrack (pending)

## Важно

- Обязательно указывать `env.project` и `env.environment` при запуске
- `env.assign` должен быть валидным email или identifier пользователя JIRA
- Все операции с JIRA выполняются через MCP сервер Atlassian
- Контекст контура (SIGMA/DELTA/SBERTRACK) сохраняется в задаче для идентификации среды
- Выбор MCP сервера зависит от контура и выполняется автоматически в зависимости от `env.environment`