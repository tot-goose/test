
<div align="center">

# 🤖 Free Claude Code

### Используйте Claude Code CLI и VSCode бесплатно. Ключ API Anthropic не требуется.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)
[![Python 3.14](https://img.shields.io/badge/python-3.14-3776ab.svg?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/downloads/)
[![uv](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/uv/main/assets/badge/v0.json&style=for-the-badge)](https://github.com/astral-sh/uv)
[![Tested with Pytest](https://img.shields.io/badge/testing-Pytest-00c0ff.svg?style=for-the-badge)](https://github.com/Alishahryar1/free-claude-code/actions/workflows/tests.yml)
[![Type checking: Ty](https://img.shields.io/badge/type%20checking-ty-ffcc00.svg?style=for-the-badge)](https://pypi.org/project/ty/)
[![Code style: Ruff](https://img.shields.io/badge/code%20formatting-ruff-f5a623.svg?style=for-the-badge)](https://github.com/astral-sh/ruff)
[![Logging: Loguru](https://img.shields.io/badge/logging-loguru-4ecdc4.svg?style=for-the-badge)](https://github.com/Delgan/loguru)

Легковесный прокси-сервер, который перенаправляет вызовы API Anthropic от Claude Code в **NVIDIA NIM** (40 запросов/мин бесплатно), **OpenRouter** (сотни моделей), **DeepSeek** (прямой API, совместимый с Anthropic), **LM Studio** (полностью локально), **llama.cpp** (локально с эндпоинтами Anthropic) или **Ollama** (полностью локально, нативная поддержка Anthropic Messages).

[Quick Start](#quick-start) · [Providers](#providers) · [Discord Bot](#discord-bot) · [Configuration](#configuration) · [Development](#development) · [Contributing](#contributing)

---

</div>

<div align="center">
  <img src="pic.png" alt="Free Claude Code в действии" width="700">
  <p><em>Claude Code запущен через NVIDIA NIM, полностью бесплатно</em></p>
</div>

## Возможности (Features)

| Возможность               | Описание                                                                                     |
| ------------------------- | ----------------------------------------------------------------------------------------------- |
| **Нулевая стоимость**     | 40 запросов/мин бесплатно на NVIDIA NIM. Бесплатные модели на OpenRouter. Полностью локально с LM Studio, Ollama или llama.cpp |
| **Прямая замена**         | Установите 2 переменные окружения. Модификации Claude Code CLI или расширения VSCode не требуются                  |
| **6 провайдеров**         | NVIDIA NIM, OpenRouter, DeepSeek, LM Studio (локально), llama.cpp (`llama-server`), Ollama         |
| **Отображение по моделям**| Перенаправляйте запросы Opus / Sonnet / Haiku на разные модели и провайдеры. Свободно смешивайте провайдеры             |
| **Поддержка мышления**     | Разбирает теги `</think>` и поля `reasoning_content` в нативные блоки мышления Claude                |
| **Эвристический парсер**  | Вызовы инструментов, выводимые моделями как текст, автоматически парсятся в структурированные вызовы                   |
| **Оптимизация запросов**  | 5 категорий простых API-вызовов перехватываются локально, экономя квоту и задержку                 |
| **Умное ограничение**     | Проактивное регулирование в скользящем окне + реактивный экспоненциальный откат при 429 + опциональный лимит параллелизма |
| **Бот Discord / Telegram**| Удаленное автономное программирование с древовидным ветвлением, сохранением сессий и прогрессом в реальном времени      |
| **Контроль субагентов**   | Перехват инструмента Task принудительно устанавливает `run_in_background=False`. Никаких runaway-субагентов                   |
| **Расширяемость**         | Чистые ABC `BaseProvider` и `MessagingPlatform`. Легко добавляйте новые провайдеры или платформы        |

## Быстрый старт (Quick Start)

### Предварительные требования

1. Получите API-ключ (или используйте локального провайдера):
   - **NVIDIA NIM**: [build.nvidia.com/settings/api-keys](https://build.nvidia.com/settings/api-keys)
   - **OpenRouter**: [openrouter.ai/keys](https://openrouter.ai/keys)
   - **DeepSeek**: [platform.deepseek.com/api_keys](https://platform.deepseek.com/api_keys)
   - **LM Studio**: API-ключ не нужен. Запустите локально с [LM Studio](https://lmstudio.ai)
   - **llama.cpp**: API-ключ не нужен. Запустите `llama-server` локально.
   - **Ollama**: API-ключ не нужен. Запустите локально с [Ollama](https://ollama.com) (`ollama serve`).
2. Установите [Claude Code](https://github.com/anthropics/claude-code)

### Установка `uv`

```bash
# Рекомендуемый установщик (работает на macOS/Linux без использования системного pip)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Обновите uv, если он уже установлен
uv self update

# Этому проекту требуется Python 3.14
uv python install 3.14
```

PowerShell (Windows):

```powershell
# Рекомендуемый установщик (избегает использования системного pip)
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# Обновите uv, если он уже установлен
uv self update

# Этому проекту требуется Python 3.14
uv python install 3.14
```

`pip install uv` может не сработать на Python, управляемом Homebrew, с ошибкой `externally-managed-environment` (PEP 668), поэтому предпочтитель использовать официальный установщик выше.

### Клонирование и настройка

```bash
git clone https://github.com/Alishahryar1/free-claude-code.git
cd free-claude-code
cp .env.example .env
```

Выберите своего провайдера и отредактируйте `.env`:

<details>
<summary><b>NVIDIA NIM</b> (40 запросов/мин бесплатно, рекомендуется)</summary>

```dotenv
NVIDIA_NIM_API_KEY="nvapi-your-key-here"

MODEL_OPUS=
MODEL_SONNET=
MODEL_HAIKU=
MODEL="nvidia_nim/z-ai/glm4.7"                     # запасной вариант

# Переключатели для каждой модели Claude для запросов рассуждения провайдера и блоков мышления Claude.
# Пустые переключатели наследуют ENABLE_MODEL_THINKING.
ENABLE_OPUS_THINKING=
ENABLE_SONNET_THINKING=
ENABLE_HAIKU_THINKING=
ENABLE_MODEL_THINKING=true
```

</details>

<details>
<summary><b>OpenRouter</b> (сотни моделей)</summary>

```dotenv
OPENROUTER_API_KEY="sk-or-your-key-here"

MODEL_OPUS="open_router/deepseek/deepseek-r1-0528:free"
MODEL_SONNET="open_router/openai/gpt-oss-120b:free"
MODEL_HAIKU="open_router/stepfun/step-3.5-flash:free"
MODEL="open_router/stepfun/step-3.5-flash:free"     # запасной вариант
```

</details>

<details>
<summary><b>DeepSeek</b> (прямой API)</summary>

```dotenv
DEEPSEEK_API_KEY="your-deepseek-key-here"

MODEL_OPUS="deepseek/deepseek-reasoner"
MODEL_SONNET="deepseek/deepseek-chat"
MODEL_HAIKU="deepseek/deepseek-chat"
MODEL="deepseek/deepseek-chat"                      # запасной вариант
```

</details>

<details>
<summary><b>LM Studio</b> (полностью локально, без API-ключа)</summary>

```dotenv
MODEL_OPUS="lmstudio/unsloth/MiniMax-M2.5-GGUF"
MODEL_SONNET="lmstudio/unsloth/Qwen3.5-35B-A3B-GGUF"
MODEL_HAIKU="lmstudio/unsloth/GLM-4.7-Flash-GGUF"
MODEL="lmstudio/unsloth/GLM-4.7-Flash-GGUF"         # запасной вариант
```

</details>

<details>
<summary><b>llama.cpp</b> (полностью локально, без API-ключа)</summary>

```dotenv
LLAMACPP_BASE_URL="http://localhost:8080/v1"

MODEL_OPUS="llamacpp/local-model"
MODEL_SONNET="llamacpp/local-model"
MODEL_HAIKU="llamacpp/local-model"
MODEL="llamacpp/local-model"
```

</details>

<details>
<summary><b>Ollama</b> (полностью локально, без API-ключа)</summary>

```dotenv
OLLAMA_BASE_URL="http://localhost:11434"

MODEL_OPUS="ollama/llama3.1"
MODEL_SONNET="ollama/llama3.1"
MODEL_HAIKU="ollama/llama3.1"
MODEL="ollama/llama3.1"                             # запасной вариант
```

Установка: [ollama.com](https://ollama.com). Скачайте модель (`ollama pull llama3.1`) и держите сервер запущенным (`ollama serve` или десктопное приложение). Используйте тот же тег модели в `MODEL*`, что указан в `ollama list` (например `ollama/llama3.1:8b`).

</details>

<details>
<summary><b>Смешивание провайдеров</b></summary>

Каждая переменная `MODEL_*` может использовать разный провайдер. `MODEL` — это запасной вариант для нераспознанных моделей Claude.

```dotenv
NVIDIA_NIM_API_KEY="nvapi-your-key-here"
OPENROUTER_API_KEY="sk-or-your-key-here"

MODEL_OPUS="nvidia_nim/moonshotai/kimi-k2.5"
MODEL_SONNET="open_router/deepseek/deepseek-r1-0528:free"
MODEL_HAIKU="lmstudio/unsloth/GLM-4.7-Flash-GGUF"
MODEL="nvidia_nim/z-ai/glm4.7"                      # запасной вариант
```

</details>

> Миграция: `NIM_ENABLE_THINKING` и `ENABLE_THINKING` были удалены в этом релизе. Используйте `ENABLE_MODEL_THINKING` как основной переключатель, с опциональными переопределениями `ENABLE_OPUS_THINKING`, `ENABLE_SONNET_THINKING` и `ENABLE_HAIKU_THINKING`.

<details>
<summary><b>Опциональная аутентификация</b> (ограничить доступ к вашему прокси)</summary>

Установите `ANTHROPIC_AUTH_TOKEN` в `.env`, чтобы требовать от клиентов аутентификацию:

```dotenv
ANTHROPIC_AUTH_TOKEN="your-secret-token-here"
```

**Как это работает:**
- Если `ANTHROPIC_AUTH_TOKEN` пуст (по умолчанию), аутентификация не требуется (обратная совместимость)
- Если установлено, клиенты должны предоставить тот же токен через заголовок `ANTHROPIC_AUTH_TOKEN`
- Скрипт `claude-pick` автоматически считывает токен из `.env`, если он настроен

**Пример использования:**
```bash
# С аутентификацией
ANTHROPIC_AUTH_TOKEN="your-secret-token-here" \
ANTHROPIC_BASE_URL="http://localhost:8082" claude

# claude-pick автоматически использует настроенный токен
claude-pick
```

Используйте эту функцию, если:
- Прокси запущен в публичной сети
- Вы делитесь сервером с другими, но хотите ограничить доступ
- Хотите дополнительный уровень безопасности

</details>

### Запуск

**Терминал 1:** Запустите прокси-сервер:

```bash
uv run uvicorn server:app --host 0.0.0.0 --port 8082
```

**Терминал 2:** Запустите Claude Code:

Укажите `ANTHROPIC_BASE_URL` на корневой URL прокси, а не на `http://localhost:8082/v1`.

#### Powershell
```powershell
$env:ANTHROPIC_AUTH_TOKEN="freecc"; $env:ANTHROPIC_BASE_URL="http://localhost:8082"; claude
```
#### Bash
```bash
ANTHROPIC_AUTH_TOKEN="freecc" ANTHROPIC_BASE_URL="http://localhost:8082" claude
```

Вот и все! Теперь Claude Code использует ваш настроенный провайдер бесплатно.

<details>
<summary><b>Настройка расширения VSCode</b></summary>

1. Запустите прокси-сервер (как указано выше).
2. Откройте Настройки (`Ctrl + ,`) и найдите `claude-code.environmentVariables`.
3. Нажмите **Edit in settings.json** и добавьте:

```json
"claudeCode.environmentVariables": [
  { "name": "ANTHROPIC_BASE_URL", "value": "http://localhost:8082" },
  { "name": "ANTHROPIC_AUTH_TOKEN", "value": "freecc" }
]
```

4. Перезагрузите расширения.
5. **Если вы видите экран входа**: Нажмите **Anthropic Console**, затем авторизуйтесь. Расширение начнет работать. Вас могут перенаправить в браузер для покупки кредитов — игнорируйте это, расширение уже работает.

Чтобы вернуться к моделям Anthropic, закомментируйте добавленный блок и перезагрузите расширения.

</details>


<details>
<summary><b>Настройка расширения IntelliJ</b></summary>

1. Откройте файл конфигурации:
   - **Windows**: `C:\Users\%USERNAME%\AppData\Roaming\JetBrains\acp-agents\installed.json`
   - **Linux/macOS**: `~/.jetbrains/acp.json`
2. Внутри `acp.registry.claude-acp` измените:

   ```
   "env": {}
   ```
   на

   ```
   "env": {
   "ANTHROPIC_AUTH_TOKEN": "freecc",
   "ANTHROPIC_BASE_URL": "http://localhost:8082"
   }
   ```
3. Запустите прокси-сервер
4. Перезапустите IDE

</details>

<details>
<summary><b>Поддержка нескольких моделей (Выбор модели)</b></summary>

`claude-pick` — это интерактивный селектор моделей, который позволяет выбирать любую модель из вашего активного провайдера при каждом запуске Claude, без редактирования `MODEL` в `.env`.

https://github.com/user-attachments/assets/9a33c316-90f8-4418-9650-97e7d33ad645

**1. Установите [fzf](https://github.com/junegunn/fzf)**:

```bash
brew install fzf        # macOS/Linux
```

**2. Добавьте алиас в `~/.zshrc` или `~/.bashrc`:**

```bash
alias claude-pick="/absolute/path/to/free-claude-code/claude-pick"
```

Затем перезагрузите ваш shell (`source ~/.zshrc` или `source ~/.bashrc`) и запустите `claude-pick`.

**Или используйте алиас с фиксированной моделью** (пикер не нужен):

```bash
alias claude-kimi='ANTHROPIC_BASE_URL="http://localhost:8082" ANTHROPIC_AUTH_TOKEN="freecc:moonshotai/kimi-k2.5" claude'
```

</details>

### Установка как пакета (клонирование не требуется)

```bash
uv tool install git+https://github.com/Alishahryar1/free-claude-code.git
fcc-init        # создает ~/.config/free-claude-code/.env из встроенного шаблона
```

Отредактируйте `~/.config/free-claude-code/.env` с вашими API-ключами и именами моделей, затем:

```bash
free-claude-code    # запускает сервер
```

> Для обновления: `uv tool upgrade free-claude-code`

---

## Как это работает

```
┌─────────────────┐        ┌──────────────────────┐        ┌──────────────────┐
│  Claude Code    │───────>│  Free Claude Code    │───────>│  LLM Provider    │
│  CLI / VSCode   │<───────│  Proxy (:8082)       │<───────│  NIM / OR / LMS  │
└─────────────────┘        └──────────────────────┘        └──────────────────┘
   Anthropic API                                             Native Anthropic
   format (SSE)                                             или OpenAI chat SSE
```

- **Прозрачный прокси**: Claude Code отправляет стандартные запросы API Anthropic; прокси перенаправляет их на ваш настроенный провайдер
- **Маршрутизация по моделям**: Запросы Opus / Sonnet / Haiku направляются на свой специфический бэкенд, с `MODEL` как запасным вариантом
- **Оптимизация запросов**: 5 категорий простых запросов (проверка квоты, генерация заголовка, определение префикса, предложения, извлечение путей файлов) перехватываются и обрабатываются локально без использования квоты API
- **Обработка формата**: OpenRouter, LM Studio, llama.cpp и Ollama используют нативные эндпоинты Anthropic Messages; NIM и DeepSeek используют общую трансляцию чата OpenAI
- **Токены мышления**: Теги `</think>` и поля `reasoning_content` преобразуются в нативные блоки мышления Claude, когда переключатель мышления для разрешенной модели включен

Прокси также предоставляет совместимые с Claude маршруты для зондирования: `GET /v1/models`, `POST /v1/messages`, `POST /v1/messages/count_tokens`, плюс поддержку `HEAD`/`OPTIONS` для общих эндпоинтов.

---

## Провайдеры (Providers)

| Провайдер     | Стоимость         | Лимит запросов | Лучше для                             |
| ------------- | ----------------- | -------------- | ------------------------------------ |
| **NVIDIA NIM**| Бесплатно         | 40 зап/мин     | Ежедневное использование, щедрый бесплатный тариф     |
| **OpenRouter**| Бесплатно / Платно | Варьируется    | Разнообразие моделей, запасные варианты      |
| **DeepSeek**  | По использованию  | Варьируется    | Нативные сообщения Anthropic в API DeepSeek |
| **LM Studio** | Бесплатно (локально) | Безлимитно   | Приватность, работа офлайн, без лимитов |
| **llama.cpp** | Бесплатно (локально) | Безлимитно   | Легковесный локальный движок инференса   |
| **Ollama**    | Бесплатно (локально) | Безлимитно   | Простой локальный LLM рантайм, нативный API Anthropic |

Модели используют формат с префиксом: `provider_prefix/model/name`. Неверный префикс вызывает ошибку.

| Провайдер   | Префикс `MODEL`    | Переменная API-ключа     | Base URL по умолчанию              |
| ---------- | ----------------- | -------------------- | ----------------------------- |
| NVIDIA NIM | `nvidia_nim/...`  | `NVIDIA_NIM_API_KEY` | `integrate.api.nvidia.com/v1` |
| OpenRouter | `open_router/...` | `OPENROUTER_API_KEY` | `openrouter.ai/api/v1`        |
| DeepSeek   | `deepseek/...`    | `DEEPSEEK_API_KEY`   | `api.deepseek.com/anthropic`  |
| LM Studio  | `lmstudio/...`    | (нет)               | `localhost:1234/v1`           |
| llama.cpp  | `llamacpp/...`    | (нет)               | `localhost:8080/v1`           |
| Ollama     | `ollama/...`      | (нет)               | `localhost:11434`             |

<details>
<summary><b>Модели NVIDIA NIM</b></summary>

Популярные модели (полный список в [`nvidia_nim_models.json`](nvidia_nim_models.json)):

- `nvidia_nim/minimaxai/minimax-m2.5`
- `nvidia_nim/qwen/qwen3.5-397b-a17b`
- `nvidia_nim/z-ai/glm5`
- `nvidia_nim/moonshotai/kimi-k2.5`
- `nvidia_nim/stepfun-ai/step-3.5-flash`

Обзор: [build.nvidia.com](https://build.nvidia.com/explore/discover) · Обновить список: `curl "https://integrate.api.nvidia.com/v1/models" > nvidia_nim_models.json`

</details>

<details>
<summary><b>Модели OpenRouter</b></summary>

Популярные бесплатные модели:

- `open_router/arcee-ai/trinity-large-preview:free`
- `open_router/stepfun/step-3.5-flash:free`
- `open_router/deepseek/deepseek-r1-0528:free`
- `open_router/openai/gpt-oss-120b:free`

Обзор: [openrouter.ai/models](https://openrouter.ai/models) · [Бесплатные модели](https://openrouter.ai/collections/free-models)

</details>

<details>
<summary><b>Модели DeepSeek</b></summary>

Провайдер `deepseek` использует точку входа **совместимую с Anthropic** `POST /v1/messages`
(HTTP base `https://api.deepseek.com/anthropic`), а не OpenAI `chat/completions` API. Некоторые функции запросов Anthropic не поддерживаются; см. документацию DeepSeek API для ограничений.

- `deepseek/deepseek-v4-pro` / `deepseek/deepseek-v4-flash` (рекомендуется для smoke и tools+thinking)
- `deepseek/deepseek-chat` / `deepseek/deepseek-reasoner` (старые ID моделей могут быть доступны)

Обзор: [api-docs.deepseek.com](https://api-docs.deepseek.com)

</details>

<details>
<summary><b>Модели LM Studio</b></summary>

Запускайте модели локально с [LM Studio](https://lmstudio.ai). Загрузите модель во вкладке Chat или Developer, затем установите `MODEL` в её идентификатор.

Примеры с нативной поддержкой инструментов:

- `LiquidAI/LFM2-24B-A2B-GGUF`
- `unsloth/MiniMax-M2.5-GGUF`
- `unsloth/GLM-4.7-Flash-GGUF`
- `unsloth/Qwen3.5-35B-A3B-GGUF`

Обзор: [model.lmstudio.ai](https://model.lmstudio.ai)

</details>

<details>
<summary><b>Модели llama.cpp</b></summary>

Запускайте модели локально используя `llama-server`. Убедитесь, что у вас есть GGUF с поддержкой инструментов. Установите `MODEL` в любое желаемое имя (например, `llamacpp/my-model`), так как `llama-server` игнорирует имя модели при запуске через `/v1/messages`.

Смотрите документацию Unsloth для подробных инструкций и поддерживаемых моделей:
[https://unsloth.ai/docs/models/qwen3.5#qwen3.5-small-0.8b-2b-4b-9b](https://unsloth.ai/docs/models/qwen3.5#qwen3.5-small-0.8b-2b-4b-9b)

</details>

<details>
<summary><b>Модели Ollama</b></summary>

Запускайте модели локально с [Ollama](https://ollama.com). Скачайте модель, затем установите `MODEL` в `ollama/<tag>`, где `<tag>` совпадает с именем в `ollama list` (например `ollama/llama3.1:8b` или `ollama/qwen2.5-coder:7b`).

- `OLLAMA_BASE_URL` — это **корень сервера Ollama** (по умолчанию `http://localhost:11434`). Не добавляйте `/v1`; прокси использует нативную поддержку Anthropic Messages на этом хосте.
- Изменяйте `OLLAMA_BASE_URL` только если Ollama слушает на другом адресе или порте.

```bash
ollama pull llama3.1
ollama serve   # или используйте десктопное приложение, которое поддерживает сервер запущенным
```

Обзор: [ollama.com/library](https://ollama.com/library)

</details>

---

## Discord Бот

Управляйте Claude Code удаленно через Discord (или Telegram). Отправляйте задачи, смотрите прогресс в реальном времени и управляйте множественными одновременными сессиями.

**Возможности:**

- Древовидное ветвление сообщений: ответьте на сообщение, чтобы разветвить разговор
- Сохранение сессий при перезапуске сервера
- Прямая трансляция токенов мышления, вызовов инструментов и результатов
- Неограниченное количество одновременных сессий Claude CLI (управляется через `PROVIDER_MAX_CONCURRENCY`)
- Голосовые заметки: отправляйте голосовые сообщения; они транскрибируются и обрабатываются как обычные промпты
- Команды: `/stop` (отменить задачу; ответьте на сообщение, чтобы остановить только эту задачу), `/clear` (сбросить все сессии или ответьте для очистки ветки), `/stats`

### Настройка

1. **Создайте Discord Бота**: Перейдите на [Discord Developer Portal](https://discord.com/developers/applications), создайте приложение, добавьте бота и скопируйте токен. Включите **Message Content Intent** в настройках бота.

2. **Отредактируйте `.env`:**

```dotenv
MESSAGING_PLATFORM="discord"
DISCORD_BOT_TOKEN="your_discord_bot_token"
ALLOWED_DISCORD_CHANNELS="123456789,987654321"
```

> Включите режим разработчика в Discord (Settings → Advanced), затем нажмите правой кнопкой на канал и "Copy ID". Разделяйте несколько каналов запятыми. Если пусто, никакие каналы не разрешены.

3. **Настройте рабочее пространство** (где Claude будет работать):

```dotenv
CLAUDE_WORKSPACE="./agent_workspace"
ALLOWED_DIR="C:/Users/yourname/projects"
```

4. **Запустите сервер:**

```bash
uv run uvicorn server:app --host 0.0.0.0 --port 8082
```

5. **Пригласите бота** через генератор URL OAuth2 (области: `bot`, разрешения: Read Messages, Send Messages, Manage Messages, Read Message History).

### Telegram

Установите `MESSAGING_PLATFORM=telegram` и настройте:

```dotenv
TELEGRAM_BOT_TOKEN="123456789:ABCdefGHIjklMNOpqrSTUvwxYZ"
ALLOWED_TELEGRAM_USER_ID="your_telegram_user_id"
```

Получите токен у [@BotFather](https://t.me/BotFather); найдите ваш User ID через [@userinfobot](https://t.me/userinfobot).

### Голосовые заметки

Отправляйте голосовые сообщения в Discord или Telegram; они транскрибируются и обрабатываются как обычные промпты.

| Бэкэнд                     | Описание                                                                                                   | API-ключ              |
| --------------------------- | ------------------------------------------------------------------------------------------------------------- | -------------------- |
| **Local Whisper** (по умолч.) | [Hugging Face Whisper](https://huggingface.co/openai/whisper-large-v3-turbo) — бесплатно, офлайн, совместим с CUDA | не требуется         |
| **NVIDIA NIM**              | Модели Whisper/Parakeet через gRPC                                                                              | `NVIDIA_NIM_API_KEY` |

**Установите голосовые дополнения:**

```bash
# Если вы клонировали репозиторий:
uv sync --extra voice_local          # Локальный Whisper
uv sync --extra voice                # NVIDIA NIM
uv sync --extra voice --extra voice_local  # Оба

# Если вы установили как пакет (без клонирования):
uv tool install "free-claude-code[voice_local] @ git+https://github.com/Alishahryar1/free-claude-code.git"
uv tool install "free-claude-code[voice] @ git+https://github.com/Alishahryar1/free-claude-code.git"
uv tool install "free-claude-code[voice,voice_local] @ git+https://github.com/Alishahryar1/free-claude-code.git"
```

Настройте через `WHISPER_DEVICE` (`cpu` | `cuda` | `nvidia_nim`) и `WHISPER_MODEL`. См. таблицу [Configuration](#configuration) для всех голосовых переменных и поддерживаемых значений моделей.

---

## Конфигурация

### Основное (Core)

| Переменная             | Описание                                                           | По умолчанию                                           |
| -------------------- | --------------------------------------------------------------------- | ------------------------------------------------- |
| `MODEL`              | Запасная модель (`provider/model/name`; неверный префикс → ошибка) | `nvidia_nim/z-ai/glm4.7`                          |
| `MODEL_OPUS`         | Модель для запросов Claude Opus; если пусто, используется `MODEL`           | пусто                                             |
| `MODEL_SONNET`       | Модель для запросов Claude Sonnet; если пусто, используется `MODEL`         | пусто                                             |
| `MODEL_HAIKU`        | Модель для запросов Claude Haiku; если пусто, используется `MODEL`          | пусто                                             |
| `NVIDIA_NIM_API_KEY`    | Ключ API NVIDIA                                                        | требуется для NIM                                  |
| `ENABLE_MODEL_THINKING` | Основной переключатель для запросов рассуждения и блоков мышления Claude. Установите `false`, чтобы скрыть мышление, если уровень модели не переопределяет это. | `true` |
| `ENABLE_OPUS_THINKING` | Опциональный переключатель мышления для запросов Claude Opus; если пусто, наследует `ENABLE_MODEL_THINKING`. | пусто |
| `ENABLE_SONNET_THINKING` | Опциональный переключатель мышления для запросов Claude Sonnet; если пусто, наследует `ENABLE_MODEL_THINKING`. | пусто |
| `ENABLE_HAIKU_THINKING` | Опциональный переключатель мышления для запросов Claude Haiku; если пусто, наследует `ENABLE_MODEL_THINKING`. | пусто |
| `OPENROUTER_API_KEY` | Ключ API OpenRouter                                                    | требуется для OpenRouter                           |
| `DEEPSEEK_API_KEY`   | Ключ API DeepSeek                                                      | требуется для DeepSeek                             |
| `LM_STUDIO_BASE_URL` | URL сервера LM Studio                                                  | `http://localhost:1234/v1`                        |
| `LLAMACPP_BASE_URL`  | URL сервера llama.cpp                                                  | `http://localhost:8080/v1`                        |
| `NVIDIA_NIM_PROXY`   | Опциональный URL прокси для запросов NVIDIA NIM (`http://...` или `socks5://...`) | `""` |
| `OPENROUTER_PROXY`   | Опциональный URL прокси для запросов OpenRouter (`http://...` или `socks5://...`) | `""` |
| `LMSTUDIO_PROXY`     | Опциональный URL прокси для запросов LM Studio (`http://...` или `socks5://...`) | `""` |
| `LLAMACPP_PROXY`     | Опциональный URL прокси для запросов llama.cpp (`http://...` или `socks5://...`) | `""` |
| `OLLAMA_BASE_URL`    | Корневой URL сервера Ollama                                               | `http://localhost:11434`                          |

### Ограничения и таймауты (Rate Limiting & Timeouts)

| Переменная                   | Описание                               | По умолчанию |
| -------------------------- | ----------------------------------------- | ------- |
| `PROVIDER_RATE_LIMIT`      | Запросов LLM API в окно               | `40`    |
| `PROVIDER_RATE_WINDOW`     | Окно лимита запросов (секунды)               | `60`    |
| `PROVIDER_MAX_CONCURRENCY` | Максимум одновременных открытых потоков провайдера    | `5`     |
| `HTTP_READ_TIMEOUT`        | Таймаут чтения для запросов провайдера (с)    | `120`   |
| `HTTP_WRITE_TIMEOUT`       | Таймаут записи для запросов провайдера (с)   | `10`    |
| `HTTP_CONNECT_TIMEOUT`     | Таймаут подключения для запросов провайдера (с) | `10`     |

### Мессенджеры и голос (Messaging & Voice)

| Переменная                   | Описание                                                                                                                                                        | По умолчанию             |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------- |
| `MESSAGING_PLATFORM`       | `discord` или `telegram`                                                                                                                                            | `discord`           |
| `DISCORD_BOT_TOKEN`        | Токен бота Discord                                                                                                                                                  | `""`                |
| `ALLOWED_DISCORD_CHANNELS` | ID каналов через запятую (пусто = ничего не разрешено)                                                                                                                 | `""`                |
| `TELEGRAM_BOT_TOKEN`       | Токен бота Telegram                                                                                                                                                 | `""`                |
| `ALLOWED_TELEGRAM_USER_ID` | Разрешенный ID пользователя Telegram                                                                                                                                           | `""`                |
| `CLAUDE_WORKSPACE`         | Директория, где работает агент                                                                                                                                 | `./agent_workspace` |
| `ALLOWED_DIR`              | Разрешенные директории для агента                                                                                                                                  | `""`                |
| `MESSAGING_RATE_LIMIT`     | Сообщений мессенджера в окно                                                                                                                                      | `1`                 |
| `MESSAGING_RATE_WINDOW`    | Окно мессенджера (секунды)                                                                                                                                         | `1`                 |
| `VOICE_NOTE_ENABLED`       | Включить обработку голосовых заметок                                                                                                                                         | `true`              |
| `WHISPER_DEVICE`           | `cpu` \| `cuda` \| `nvidia_nim`                                                                                                                                    | `cpu`               |
| `WHISPER_MODEL`            | Модель Whisper (локально: `tiny`/`base`/`small`/`medium`/`large-v2`/`large-v3`/`large-v3-turbo`; NIM: `openai/whisper-large-v3`, `nvidia/parakeet-ctc-1.1b-asr`, и т.д.) | `base`              |
| `HF_TOKEN`                 | Токен Hugging Face для более быстрой загрузки (локальный Whisper, опционально)                                                                                                  | —                   |

<details>
<summary><b>Продвинуто: Флаги оптимизации запросов</b></summary>

Они включены по умолчанию и перехватывают тривиальные запросы Claude Code локально для экономии квоты API.

| Переменная                          | Описание                    | По умолчанию |
| --------------------------------- | ------------------------------ | ------- |
| `FAST_PREFIX_DETECTION`           | Включить быстрое определение префикса   | `true`  |
| `ENABLE_NETWORK_PROBE_MOCK`       | Эмулировать запросы сетевого зонда    | `true`  |
| `ENABLE_TITLE_GENERATION_SKIP`    | Пропускать запросы генерации заголовка | `true`  |
| `ENABLE_SUGGESTION_MODE_SKIP`     | Пропускать запросы режима предложений  | `true`  |
| `ENABLE_FILEPATH_EXTRACTION_MOCK` | Эмулировать извлечение путей файлов       | `true`  |

</details>

Смотрите [`.env.example`](.env.example) для всех поддерживаемых параметров.

---

## Разработка (Development)

### Структура проекта

```
free-claude-code/
├── server.py              # Точка входа
├── api/                   # Маршруты FastAPI, сервисный слой API, маршрутизация моделей, обнаружение запросов, оптимизации
├── core/                  # Общие помощники протокола Anthropic, SSE, конвертация, парсеры, подсчет токенов
├── providers/             # Реестр провайдеров, состояние времени выполнения, транспорты OpenAI chat + Anthropic messages
├── messaging/             # MessagingPlatform ABC + боты Discord/Telegram, команды, голос, управление сессиями
├── config/                # Настройки, конфиг NIM, логирование
├── cli/                   # Сессия CLI и управление процессами
└── tests/                 # Набор тестов Pytest
```

### Команды

```bash
uv run ruff format     # Форматирование кода
uv run ruff check      # Линтинг
uv run ty check        # Проверка типов
uv run pytest          # Запуск тестов
```

### Расширение

**Добавление провайдера, совместимого с OpenAI** (Groq, Together AI и т.д.) — расширить `OpenAIChatTransport`, затем добавить дескриптор в реестр провайдеров:

```python
from providers.openai_compat import OpenAIChatTransport
from providers.base import ProviderConfig

class MyProvider(OpenAIChatTransport):
    def __init__(self, config: ProviderConfig):
        super().__init__(config, provider_name="MYPROVIDER",
                         base_url="https://api.example.com/v1", api_key=config.api_key)
```

**Добавление нативного провайдера Anthropic** — расширить `AnthropicMessagesTransport`, затем добавить дескриптор в `providers.registry`.

**Добавление полностью кастомного провайдера** — расширить `BaseProvider` напрямую, реализовать `stream_response()` и зарегистрировать его дескриптор.

**Добавление платформы мессенджера** — расширить `MessagingPlatform` в `messaging/` и реализовать `start()`, `stop()`, `send_message()`, `edit_message()` и `on_message()`.

---

## Вклад (Contributing)

- Сообщайте о багах или предлагайте функции через [Issues](https://github.com/Alishahryar1/free-claude-code/issues)
- Добавляйте новых провайдеров LLM (Groq, Together AI и т.д.)
- Добавляйте новые платформы мессенджеров (Slack и т.д.)
- Улучшайте покрытие тестами
- PR по интеграции с Docker пока не принимаются

```bash
git checkout -b my-feature
uv run ruff format && uv run ruff check && uv run ty check && uv run pytest
# Откройте Pull Request
```

---

## Лицензия

Лицензия MIT. Подробнее см. [LICENSE](LICENSE).

Создано с помощью [FastAPI](https://fastapi.tiangolo.com/), [OpenAI Python SDK](https://github.com/openai/openai-python), [discord.py](https://github.com/Rapptz/discord.py) и [python-telegram-bot](https://python-telegram-bot.org/).
```