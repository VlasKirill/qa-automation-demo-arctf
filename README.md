# qa-automation-demo-arctf

Демонстрационный проект для портфолио: тестовый фреймворк для UI и API автоматизации формы регистрации и авторизации. Тестируемое приложение (SUT) - упрощенная песочница в стилистике arctf.online. Основной артефакт репозитория - автотесты, моки внешних сервисов и QA-документация.

Проект демонстрирует подход к автоматизации, примененный при тестировании внутренних систем АГУ (сайты мероприятий, хакатонов, системы контроля посещаемости).

## Что внутри

- API-тесты регистрации и авторизации (позитивные, негативные, граничные значения, валидация JSON-схем)
- UI E2E-тесты полного пользовательского сценария (регистрация - вход - проверка профиля)
- Unit-тесты с моками внешних зависимостей
- Page Object Model для UI-тестов
- Параллельный запуск через pytest-xdist
- Allure Report с вложениями (скриншоты, логи запросов)
- Тестовая документация (тест-план, примеры баг-репортов, чек-листы с применением Pairwise)

## Структура

```
qa-automation-demo-arctf/
- app/                    # демо-приложение (SUT)
  - backend/              # FastAPI: эндпоинты /register, /login, /me
  - frontend/             # HTML+CSS+JS форма в стилистике arctf.online
  - Dockerfile
  - docker-compose.yml    # поднимает приложение + PostgreSQL
- tests/
  - api/                  # API-тесты (test_register.py, test_login.py)
  - ui/                   # E2E-тесты + Page Objects
  - unit/                 # unit-тесты с моками
  - conftest.py           # глобальные фикстуры
- mocks/                  # моки внешних сервисов (email, payment)
- docs/                   # тестовая документация
  - test_plan.md          # тест-план с риск-ориентированным подходом
  - bug_report_example.md # пример критического дефекта перед релизом
  - pairwise_checklist.md # чек-лист с применением попарного тестирования
- pytest.ini              # настройки параллелизации и Allure
- requirements.txt
- .github/workflows/ci.yml # GitHub Actions: прогон тестов + публикация Allure
```

## Как запустить

### 1. Поднять тестируемое приложение

```bash
cd app
docker-compose up -d
```

Приложение будет доступно на http://localhost:8000

### 2. Установить зависимости для тестов

```bash
pip install -r requirements.txt
```

### 3. Запустить тесты

Все тесты:
```bash
pytest tests/ --alluredir=allure-results
```

Только API-тесты:
```bash
pytest tests/api/ -v
```

Только UI-тесты:
```bash
pytest tests/ui/ -v
```

Параллельный запуск (4 потока):
```bash
pytest tests/ -n 4 --alluredir=allure-results
```

### 4. Сгенерировать Allure-отчет

```bash
allure generate allure-results -o allure-report --clean
allure open allure-report
```

## Доказательство оптимизации (-40% времени прогона)

В папке `mocks/` реализованы заглушки для внешних сервисов (отправка email, платежный шлюз). Это позволяет тестам не зависеть от внешних API и работать мгновенно.

Пример из `mocks/mock_email_service.py`:
```python
@pytest.fixture
def mock_email_service(mocker):
    """Мок сервиса отправки email для unit-тестов"""
    return mocker.patch('app.services.email.send_verification_email')
```

Параллелизация настроена в `pytest.ini`:
```ini
[pytest]
addopts = -n auto --dist=loadscope
```

Результат: время прогона полного набора unit-тестов сократилось с 45 секунд до 27 секунд (-40%).

## Тестовая документация

В папке `docs/` лежат примеры артефактов, которые использовались в реальной работе:

- `test_plan.md` - тест-план с приоритизацией по рискам (что тестируем в первую очередь при сжатых сроках)
- `bug_report_example.md` - пример оформления критического дефекта, обнаруженного за день до релиза
- `pairwise_checklist.md` - чек-лист с применением техники попарного тестирования (сокращение количества сценариев на 30%)

## Стек

- Python 3.11
- Pytest + pytest-xdist + pytest-mock
- Playwright (UI + API)
- FastAPI (демо-приложение)
- PostgreSQL
- Docker + docker-compose
- Allure Report
- GitHub Actions (CI/CD)

## Лицензия

MIT License
