# 🌐 IT-Forum Platform: Спецификация MVP

Единый репозиторий с архитектурой, требованиями и артефактами для веб-приложения IT-форума (Django монолит).

## 📑 Навигация по документации
| Раздел | Ссылка |
|--------|--------|
| Полная спецификация требований | [docs/specification/requirements.md](docs/specification/requirements.md) |
| Flow авторизации OAuth2/OIDC | [docs/architecture/oauth_flow.md](docs/architecture/oauth_flow.md) |
| Моки провайдеров для тестов | [docs/testing/mock_responses.json](docs/testing/mock_responses.json) |
| Чек-лист OWASP & WCAG 2.1 AA | [docs/security/owasp_wcag_checklist.md](docs/security/owasp_wcag_checklist.md) |
| История изменений | [CHANGELOG.md](CHANGELOG.md) |

## 🎯 Статус MVP
| Модуль | Статус | Примечание |
|--------|--------|------------|
| Аутентификация (Email + OAuth) | 🟢 Утверждено | Google, Яндекс, Mail.ru, GitHub |
| Управление проектами | 🟢 Утверждено | CRUD, RBAC, статусы |
| Форум Q&A (вложенность 3) | 🟢 Утверждено | Soft-delete, модерация |
| Заявки & Приглашения | 🟢 Утверждено | Контакты доступны после принятия |
| Email-уведомления | 🟢 Утверждено | Celery + SMTP/REST API |
| Безопасность & Доступность | 🟢 Утверждено | OWASP Top 10, WCAG 2.1 AA |

## 🛠 Технологический стек
- **Backend**: Python 3.11+, Django 5.x, `django-allauth`, Celery
- **БД/Кэш**: PostgreSQL 15+, Redis 7
- **Frontend**: Django Templates + HTMX/Alpine.js
- **Инфраструктура**: Gunicorn, Nginx, Docker, GitHub Actions (CI)

## 📜 Лицензия
MIT License © 2024. См. [LICENSE](LICENSE)
