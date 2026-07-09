# 📘 Спецификация требований: IT-Forum MVP

## 1. Общие сведения
- **Назначение**: Платформа для поиска IT-проектов, формирования команд и обмена опытом.
- **Архитектура**: Монолит на Django, горизонтальное масштабирование через stateless-инстансы.
- **Соответствие**: 152-ФЗ, OWASP Top 10, WCAG 2.1 AA.

## 2. Функциональные требования
### 2.1 Пользователи
- Регистрация/вход по Email+Пароль (верификация обязательна)
- OAuth2: Google, Яндекс, Mail.ru, GitHub (`django-allauth`)
- Профиль: навыки, стек, роль, часовой пояс, статус доступности
- Привязка соцсетей к существующему аккаунту

### 2.2 Проекты
- CRUD с полями: название, описание, статус (`поиск`, `в работе`, `закрыт`), роли, стек, тип занятости, дедлайн
- Роли: `Owner`, `CoAdmin`, `Member`
- Контакты доступны: владельцу до приглашения, участникам после принятия заявки

### 2.3 Форум Q&A
- Структура: Категория → Тема → Сообщение (макс. 3 уровня вложенности)
- Действия: создание, ответ, редактирование (с пометкой `изменено`), мягкое удаление, жалобы
- Поиск: полнотекстовый (PostgreSQL `pg_trgm`), фильтры по тегам/ролям/статусу

### 2.4 Заявки & Уведомления
- Подача заявки на участие → статус `pending/accepted/rejected`
- Email-уведомления: верификация, заявки, ответы в темах, еженедельный дайджест
- Настройка частоты и типов уведомлений в ЛК

## 3. Нефункциональные требования
| Категория | Требование |
|-----------|------------|
| Производительность | P95 ≤ 1.5 сек, ≥ 5 000 активных сессий |
| Надёжность | Uptime 99.9%, retry-очередь для email |
| Безопасность | HTTPS, CSRF/XSS/SQLi защита, rate-limit, Argon2 хеширование |
| Доступность | WCAG 2.1 AA, mobile-first, контраст ≥ 4.5:1 |
| Локализация | ru (default), en (i18n) |

## 4. Модель данных (ключевые сущности)
- `User(AbstractUser)`: email, skills(M2M), notification_prefs(JSON)
- `Project`: title, status(enum), owner(FK), required_roles(JSON), is_archived(bool)
- `Application`: project(FK), applicant(FK), status(enum), message
- `ForumPost`: category(FK), author(FK), parent(FK, self), is_deleted(bool)
- `NotificationLog`: user, type, template, status, sent_at

> Индексы: GIN для поиска, B-Tree по slug/status. Мягкое удаление для аудита.

## 5. Интеграции
- **Почта**: Celery task + exponential backoff, идемпотентность по `message_id`, DLQ при ошибках
- **OAuth**: Authorization Code Flow, OIDC для Google/Яндекс, валидация `state`/`nonce`, проверка `id_token`
- **Хранение медиа**: S3-совместимое через `django-storages`

## 6. Безопасность и доступность
Полный чек-лист реализации: [docs/security/owasp_wcag_checklist.md](../security/owasp_wcag_checklist.md)

## 7. Тестирование
- Unit: `pytest-django` ≥80% покрытия бизнес-логики
- Integration: mock OAuth endpoints, email queue assertions
- E2E: Playwright (регистрация, заявка, форум)
- Security: OWASP ZAP, `bandit`, `pip-audit`
- A11y: `axe-core` в CI, ручные тесты с NVDA/VoiceOver

## 8. Допущения MVP
- Токены OAuth не используются для downstream API
- Обновление `refresh_token` не автоматизировано (повторный логин при истечении)
- Real-time чат вынесен за скопы v1
- Хранение данных на серверах РФ (опционально при работе с 152-ФЗ)
