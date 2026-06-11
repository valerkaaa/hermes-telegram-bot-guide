---
tags: [hermes, telegram, руководство, настройка, gateway, боты]
created: 2026-06-10
---

# 🤖 Полное руководство: Telegram бот для Hermes Agent

> **Цель:** Подключить Telegram бота к Hermes Agent, чтобы общаться с AI-агентом из любого места — с телефона, планшета, другого ПК.
> **Сложность:** 🟢 Начальная (справится любой)
> **Время:** ~15-20 минут

---

## 📋 Что мы получим в итоге

| Возможность | Описание |
|-------------|----------|
| 💬 **Текстовые сообщения** | Пишешь боту — отвечает Hermes |
| 🎤 **Голосовые сообщения** | Отправляешь войс — бот распознаёт речь |
| 🖼️ **Изображения** | Отправляешь фото — бот видит и анализирует |
| 📁 **Файлы** | Отправляешь документы — бот читает |
| 📅 **Cron-задачи** | Результаты расписаний приходят в Telegram |
| 👥 **Групповые чаты** | Бот может работать в группе |
| 🌐 **Кросс-платформа** | Чат в Telegram = те же возможности, что и в терминале |

---

## 📦 Требования

Перед началом убедись, что:

- ✅ **Hermes Agent установлен** и работает (`hermes --version` показывает версию)
- ✅ **Установлен Python** (3.10 или новее)
- ✅ **Установлен pip** / **uv** для зависимостей
- ✅ **Есть аккаунт в Telegram**

---

## 🧭 Структура руководства

```
├── Шаг 1️⃣  — Создать бота в Telegram (@BotFather)
├── Шаг 2️⃣  — (Опционально) Настроить внешний вид бота
├── Шаг 3️⃣  — Узнать свой Telegram User ID
├── Шаг 4️⃣  — Добавить токен и ID в конфиг Hermes
├── Шаг 5️⃣  — Установить зависимости для Telegram
├── Шаг 6️⃣  — Запустить Gateway (шлюз)
├── Шаг 7️⃣  — Проверка: написать боту
├── Шаг 8️⃣  — Настройка для группы
├── Шаг 9️⃣  — Решение типичных проблем
└── Шаг 🔟  — Полезные команды в Telegram
```

---

## Шаг 1️⃣: Создать бота в Telegram через @BotFather

> [!info] Что такое BotFather?
> **@BotFather** — это официальный бот Telegram для создания и управления другими ботами. Он выдаёт токены API, которые позволяют программам (вроде Hermes) подключаться к Telegram.

### Пошаговая инструкция

**1.** Открой Telegram на любом устройстве (телефон, ПК, веб)

**2.** Найди **@BotFather**:
   - Введи в поиске: `@BotFather`
   - Или перейди по ссылке: [t.me/BotFather](https://t.me/BotFather)
   - Официальный BotFather имеет ✅ **синюю галочку** верификации

**3.** Напиши команду:
   ```
   /newbot
   ```

**4.** BotFather попросит **название бота** (display name):
   ```
   Alright, a new bot. How are we going to call it? Please choose a name for your bot.
   ```
   Введи, например:
   ```
   My Assistant Bot
   ```
   > Название может быть любым, на русском или английском. Пользователи увидят его в списке чатов.

**5.** Затем BotFather попросит **username** (техническое имя):
   ```
   Good. Now let's choose a username for your bot. It must end in 'bot'. Like this, for example: TetrisBot or tetris_bot.
   ```
   Введи уникальное имя, обязательно заканчивающееся на `bot`:
   ```
   my_assistant_bot
   ```
   > Username используется в ссылках: `t.me/my_assistant_bot`
   > Должен быть уникальным во всём Telegram. Если занято — попробуй другой вариант.

**6.** BotFather ответит:
   ```
   Done! Congratulations on your new bot. You will find it at:
   t.me/my_assistant_bot

   Use this token to access the HTTP API:
   123456789:ABCdefGHIjklMNOpqrSTUvwxYZ
   ```
   > [!warning] ⚠️ Сохрани токен!
   > Токен — это **пароль** от твоего бота. Никому его не показывай.
   > - Выглядит как `число:буквы_и_цифры`
   > - Если токен утек — немедленно сделай `/revoke` в BotFather, затем `/token` чтобы получить новый
   > - Токен понадобится на **Шаге 4**

**7.** Сразу же открой своего бота по ссылке `t.me/твой_бот` и нажми **START** — это активирует диалог

---

## Шаг 2️⃣: (Опционально) Настроить внешний вид бота

> [!tip] Зачем это нужно?
> Красиво оформленный бот выглядит профессионально, понятно объясняет новым пользователям свои возможности и вызывает доверие. Особенно если планируешь делиться ботом с другими.

Вернись в @BotFather и используй эти команды:

### `/setdescription` — описание бота

```
/setdescription
→ Выбери бота
→ Введи описание (до 512 символов)
```

Пример:
```
🤖 Персональный AI-ассистент.
Помогаю с:
• Разработкой и кодом
• Исследованиями и анализом
• Ответами на любые вопросы
Напиши мне — я онлайн 24/7 🚀
```

### `/setabouttext` — краткое описание (в профиле)

```
/setabouttext
→ Выбери бота
→ Введи текст (до 120 символов)
```

Пример:
```
AI-ассистент: помощь в разработке, аналитике, исследованиях. Пиши!
```

### `/setuserpic` — аватар бота

```
/setuserpic
→ Выбери бота
→ Отправь изображение (квадратное, 512x512 px)
```

### `/setcommands` — меню команд

```
/setcommands
→ Выбери бота
→ Отправь список команд
```

Рекомендую:
```
help — 📖 Помощь и список команд
new — 🆕 Начать новый разговор
topic — 💬 Управление темами (в группах)
speed — ⚡ Режим ответа: быстрый/полный
model — 🧠 Показать текущую модель
status — 📊 Информация о сессии
```

> [!info] Как выглядят команды в Telegram
> После настройки, когда пользователь вводит `/` в чате с ботом — появляется выпадающее меню с твоими командами и описаниями.

---

## Шаг 3️⃣: Узнать свой Telegram User ID

> [!warning] Почему это нужно?
> Бот должен отвечать **только тебе** (или списку разрешённых пользователей). Если не настроить `TELEGRAM_ALLOWED_USERS` — бот будет игнорировать все входящие сообщения. **Это защита от спама и чужих людей.**

### Как получить ID

**Способ 1: @userinfobot** (проще всего)

1. Найди бота **@userinfobot** (или [t.me/userinfobot](https://t.me/userinfobot))
2. Напиши ему любое сообщение
3. Получишь ответ:
   ```
   Id: 123456789
   First name: Имя
   Language: ru
   ```

**Способ 2: @getmyid_bot**
1. Найди **@getmyid_bot**
2. Напиши `/start`
3. Он покажет твой ID

> [!warning] ⚠️ Не перепутай!
> - `TELEGRAM_ALLOWED_USERS` = твой **личный числовой ID** (узнаёшь через @userinfobot)
> - НЕ ID бота
> - НЕ username (@твой_ник)
> - Если поставишь ID бота — бот не сможет тебе ответить (Telegram запрещает ботам писать ботам, будет ошибка `403 Forbidden`)

### Несколько пользователей

Если хочешь разрешить бота для нескольких человек (например, ты + коллега):

1. Каждый узнаёт свой ID через @userinfobot
2. В конфиге перечисляешь через запятую:
   ```
   TELEGRAM_ALLOWED_USERS=123456789,987654321,555666777
   ```

---

## Шаг 4️⃣: Добавить токен и ID в конфиг Hermes

Теперь нужно «познакомить» Hermes с Telegram — записать токен и твой ID в `.env` файл.

> [!info] Что такое `.env`?
> Файл `.env` хранит секретные ключи и токены. Он находится в папке `~/.hermes/` и содержит все API-ключи для разных сервисов: OpenRouter, Anthropic, Telegram и т.д.

### Вариант А: Создать .env через терминал (рекомендую)

Открой терминал и выполни эту команду (замени **СВОЁ** на реальные значения):

```bash
cat > ~/.hermes/.env << 'EOF'
# 🤖 Hermes Agent — Environment Variables

# === TELEGRAM ===
TELEGRAM_BOT_TOKEN=123456789:ABCdefGHIjklMNOpqrSTUvwxYZ
TELEGRAM_ALLOWED_USERS=123456789

# === MODEL PROVIDER ===
# OPENROUTER_API_KEY=sk-or-v1-...
# ANTHROPIC_API_KEY=sk-ant-...
# DEEPSEEK_API_KEY=sk-...
EOF
```

> [!tip] Подсказка для Windows (git-bash)
> Путь `~/.hermes` в git-bash обычно соответствует `C:\Users\<имя_пользователя>\AppData\Local\hermes\`
> Если не работает — используй абсолютный путь:
> `/c/Users/<имя_пользователя>/AppData/Local/hermes/.env`

### Вариант Б: Создать .env через Блокнот

Если неудобно работать в терминале:

1. Открой Проводник: `C:\Users\<имя_пользователя>\AppData\Local\hermes\`
2. Создай новый файл с именем `.env` (важно: **без расширения**, не `.env.txt`)
3. Вставь содержимое:
   ```
   TELEGRAM_BOT_TOKEN=123456789:ABCdefGHIjklMNOpqrSTUvwxYZ
   TELEGRAM_ALLOWED_USERS=123456789
   ```
4. Сохрани и закрой

> [!warning] Если .env уже существует
> Не удаляй старые ключи — просто допиши Telegram строки в конец файла!

### Проверка

Убедись, что файл создался корректно:

```bash
cat ~/.hermes/.env
```

Должен увидеть строки с токеном и ID.

### Дополнительные настройки (в config.yaml)

Если нужно настроить Telegram детальнее — отредактируй `~/.hermes/config.yaml`:

```yaml
gateway:
  platforms:
    telegram:
      enabled: true
      # topics: false            # Включить темы в группах
      # max_message_length: 4096 # Максимальная длина ответа
      # parse_mode: MarkdownV2   # Форматирование (MarkdownV2 / HTML)
```

> Обычно это не требуется — стандартные настройки работают «из коробки».

---

## Шаг 5️⃣: Установить зависимости для Telegram

Hermes использует библиотеку `python-telegram-bot` для работы с Telegram. Проверим, установлена ли она:

```bash
# Проверка
python -c "import telegram" 2>&1

# Если ошибка — установка
pip install python-telegram-bot --upgrade

# Или через uv (если он есть)
uv pip install python-telegram-bot --upgrade
```

> [!tip] Дополнительные пакеты
> Для голосовых сообщений (STT) потребуется `faster-whisper`:
> ```bash
> pip install faster-whisper
> ```
> Подробнее: `hermes config set stt.provider local`

---

## Шаг 6️⃣: Запустить Gateway (шлюз)

> [!info] Что такое Gateway?
> Gateway (шлюз) — это фоновый процесс Hermes, который слушает входящие сообщения с платформ (Telegram, Discord и т.д.), передаёт их AI-агенту и отправляет ответы обратно. Работает 24/7.

### Вариант А: Ручной запуск (для проверки)

```bash
hermes gateway run
```

Gateway запустится **в текущем окне терминала**:
- Ты увидишь логи в реальном времени
- Работает, пока не закроешь терминал или не нажмёшь `Ctrl+C`
- **Идеально для первого теста**

Выглядит так:
```
[2026-06-10 12:00:00] INFO     Starting Gateway...
[2026-06-10 12:00:01] INFO     Telegram: Connected as @my_assistant_bot
[2026-06-10 12:00:01] INFO     Gateway is running. Press Ctrl+C to stop.
```

### Вариант Б: Как служба (для постоянной работы)

После проверки запусти как фоновую службу:

```bash
# Установить как задачу ОС (автозапуск при входе)
hermes gateway install

# Запустить
hermes gateway start

# Проверить статус
hermes gateway status
```

Должен увидеть:
```
✓ Gateway is running
```

### Вариант В: Если gateway install не работает (Linux/macOS)

```bash
nohup hermes gateway run > ~/.hermes/logs/gateway.log 2>&1 &
```

Чтобы остановить потом:
```bash
# Найти процесс
ps aux | grep "hermes gateway"

# Убить
kill [PID]
```

На Windows — можно через **Планировщик заданий** добавить запуск `hermes gateway run` при входе в систему.

---

## Шаг 7️⃣: Проверка — написать боту

### Если Gateway запущен вручную (Вариант А)

Оставь терминал с gateway открытым, открой Telegram на телефоне или ПК:

1. Найди своего бота по username (например `@my_assistant_bot`)
2. Напиши: `Привет!`
3. Через 2-10 секунд должен прийти ответ

### Если Gateway запущен как служба (Вариант Б)

Просто открой Telegram и напиши боту.

### Что ты увидишь

**В Telegram:**
```
Ты: Привет!
Бот: Привет! Я Hermes Agent, твой AI-ассистент. Чем могу помочь?
```

**В терминале (где запущен gateway):**
```
[2026-06-10 12:05:00] INFO     Received message from @username (123456789): "Привет!"
[2026-06-10 12:05:03] INFO     Sending response...
[2026-06-10 12:05:04] INFO     Response sent successfully
```

Теперь ты можешь общаться с Hermes Agent через Telegram с телефона!

---

## 🎤 Голосовые сообщения в Telegram

### Входящие голосовые (Speech-to-Text)

Голосовые сообщения автоматически транскрибируются в текст выбранным STT провайдером:

| Провайдер | Требует ключ | Команда |
|-----------|--------------|---------|
| **local** (faster-whisper) | ❌ | `hermes config set stt.provider local` |
| **groq** | ✅ GROQ_API_KEY | `hermes config set stt.provider groq` |
| **openai** | ✅ VOICE_TOOLS_OPENAI_KEY | `hermes config set stt.provider openai` |

**Установка local STT:**
```bash
pip install faster-whisper
hermes config set stt.provider local
hermes config set stt.local.model small
```

### Исходящие голосовые (Text-to-Speech)

Ответы приходят как голосовые сообщения.

| Провайдер | Команда |
|-----------|---------|
| **edge** (по умолчанию) | `hermes config set tts.provider edge` |
| **elevenlabs** | `hermes config set tts.provider elevenlabs` |

**Настройка русского голоса:**
```bash
hermes config set tts.edge.voice ru-RU-DmitryNeural
```

**Для больших файлов (>20 МБ):**
Telegram Bot API ограничивает загрузку 20 МБ. Для больших файлов запустите локальный `telegram-bot-api` сервер.

---

## Шаг 8️⃣: Настройка для групповых чатов

Если хочешь добавить бота в группу (например, для командной работы):

### 1. Настрой приватность

В BotFather:
```
/mybots → выбери бота → Bot Settings → Group Privacy → Turn off
```

> [!info] Что это даёт?
> По умолчанию бот в группе **видит только команды** (начинающиеся с `/`) и ответы на свои сообщения.
> Если отключить приватность — бот будет видеть **все** сообщения в группе.

### 2. Добавь бота в группу

Открой группу → `⋮` → `Add members` → введи username бота.

### 3. (Альтернатива) Сделай бота админом

Если не хочешь отключать приватность для всех групп — можно сделать бота **администратором** группы. Тогда он тоже будет видеть все сообщения.

### 4. Команды для группы

- `/topic on` — включить темы (каждый пользователь получает отдельную тему-сессию)
- `/topic off` — отключить темы (общий чат)
- `/sethome` — сделать эту группу «домашним каналом» (сюда будут приходить результаты cron-задач)

---

## Шаг 9️⃣: Решение типичных проблем

### ❌ Бот не отвечает

**Проверь 1: Gateway запущен?**
```bash
hermes gateway status
```
Должен быть `✓ Gateway is running`

**Проверь 2: Токен правильный?**
```bash
grep TELEGRAM_BOT_TOKEN ~/.hermes/.env
```
Убедись, что токен точно скопирован (без лишних пробелов, кавычек)

**Проверь 3: ID пользователя правильный?**
```bash
grep TELEGRAM_ALLOWED_USERS ~/.hermes/.env
```
Убедись, что это **твой личный ID** (узнать через @userinfobot), а не ID бота

**Проверь 4: Логи gateway**
```bash
tail -n 50 ~/.hermes/logs/gateway.log
```
Ищи ошибки: `Error`, `FAILED`, `403`, `ConnectionError`

**Проверь 5: Зависимости**
```bash
python -c "import telegram; print(telegram.__version__)"
```

### ❌ Ошибка `403 Forbidden`

Вероятные причины:
- В `TELEGRAM_ALLOWED_USERS` указан **ID бота** — а бот не может писать боту
- Решение: проверь ID через @userinfobot, вставь правильный

### ❌ Ошибка `ConnectionError` / `Timeout`

```bash
# Проверь интернет
ping api.telegram.org -c 1

# Проверь DNS
nslookup api.telegram.org
```

### ❌ Gateway сразу падает после запуска

```bash
# Посмотри последние ошибки
tail -n 100 ~/.hermes/logs/gateway.log

# Попробуй переустановить зависимости
pip install --upgrade python-telegram-bot httpx
```

### ❌ Не приходят голосовые сообщения

```bash
# Установи faster-whisper
pip install faster-whisper

# Настрой STT
hermes config set stt.enabled true
hermes config set stt.provider local

# Перезапусти gateway
hermes gateway restart
```

### ❌ Бот отвечает, но текст «битый» (эмодзи/форматирование)

В `.env` добавь:
```bash
echo 'TELEGRAM_PARSE_MODE=HTML' >> ~/.hermes/.env
```
Или используй `MarkdownV2` вместо `HTML`.

### ❌ Gateway установлен как служба, но не стартует при входе

**Windows:**
1. Открой **Планировщик заданий** (`taskschd.msc`)
2. Найди задачу `Hermes Gateway`
3. Проверь настройки:
   - Включена ли задача
   - Правильный ли путь к Python и Hermes
   - Работает ли от текущего пользователя

**Linux (systemd):**
```bash
systemctl --user status hermes-gateway
journalctl --user -u hermes-gateway -n 30
# Включить автозапуск через systemd:
loginctl enable-linger $USER
systemctl --user enable hermes-gateway
```

---

## Шаг 🔟: Полезные команды в Telegram

После подключения бота, в Telegram работают все основные команды Hermes:

### Основные

| Команда | Описание |
|---------|----------|
| `/help` | Показать список команд |
| `/new` | Начать новый разговор (сбросить контекст) |
| `/retry` | Переотправить последний запрос |
| `/undo` | Отменить последний обмен |
| `/title новое название` | Переименовать текущую сессию |
| `/stop` | Остановить фоновые процессы |
| `/model` | Показать текущую модель |
| `/fast` | Переключить быстрый режим |

### Gateway

| Команда | Описание |
|---------|----------|
| `/sethome` | Сделать этот чат домашним каналом |
| `/topic on/off` | Включить/отключить темы в группах |
| `/platforms` | Показать статус подключенных платформ |
| `/restart` | Перезапустить gateway |
| `/status` | Информация о сессии |

### Информация

| Команда | Описание |
|---------|----------|
| `/usage` | Статистика использования токенов |
| `/profile` | Активный профиль Hermes |
| `/debug` | Создать отчёт для отладки |
| `/insights` | Аналитика использования |

---

## 🧠 Дополнительные советы

### Персонализация (Personality)

Можешь задать боту свой стиль общения через файл `SOUL.md`:

```bash
cat > ~/.hermes/SOUL.md << 'EOF'
Ты — дружелюбный русскоязычный AI-ассистент.
Говоришь кратко, по делу.
Используешь эмодзи умеренно.
Отвечаешь на русском языке.
EOF
```

### Своё приветствие

При запуске gateway можно настроить приветственное сообщение в конфиге:

```yaml
# ~/.hermes/config.yaml
gateway:
  telegram:
    welcome_message: "🤖 Привет! Я твой AI-ассистент. Чем могу помочь?"
```

### Cron-задачи с доставкой в Telegram

```bash
# Пример: ежедневный дайджест в Telegram
hermes cron create "0 9 * * *" \
  --name "Утренний дайджест" \
  --prompt "Сделай краткий дайджест последних новостей из мира AI и технологий"
```

Результат будет приходить в чат с ботом автоматически.

### Несколько профилей

Можно запустить разные профили Hermes для разных задач:

```bash
hermes profile create work
hermes profile use work
# В этом профиле — свои навыки, память, конфиг
```
## 📚 Полезные ссылки

| Ресурс                             | Ссылка                                                                              |
| ---------------------------------- | ----------------------------------------------------------------------------------- |
| 📖 Официальная документация Hermes | https://hermes-agent.nousresearch.com/docs/                                         |
| 🔗 Настройка Gateway               | https://hermes-agent.nousresearch.com/docs/user-guide/messaging/                    |
| 📱 Telegram Setup | https://hermes-agent.nousresearch.com/docs/user-guide/messaging/telegram/ |
| 🤖 BotFather                       | https://t.me/BotFather                                                              |
| 🆔 @userinfobot                    | https://t.me/userinfobot                                                            |
| 💬 Python Telegram Bot             | https://github.com/python-telegram-bot/python-telegram-bot                          |

---

## 🔗 Полезные ссылки

|| Ресурс                             | Ссылка                                                                              |
|| ---------------------------------- | ----------------------------------------------------------------------------------- |
|| 📖 Официальная документация Hermes | https://hermes-agent.nousresearch.com/docs/                                         |
|| 🔌 Настройка Gateway               | https://hermes-agent.nousresearch.com/docs/user-guide/messaging/                    |
|| 📱 Telegram Setup (Official Docs) | https://hermes-agent.nousresearch.com/docs/user-guide/messaging/telegram/           |
|| 🤖 BotFather                       | https://t.me/BotFather                                                              |
|| 🆔 @userinfobot                    | https://t.me/userinfobot                                                            |
|| 💬 Python Telegram Bot             | https://github.com/python-telegram-bot/python-telegram-bot                          |

---

## 🧠 Advanced Tips (перенесено из GitHub)

### Personality Customization

Настрой стиль общения бота через файл персонажа:

```bash
cat > ~/.hermes/SOUL.md << 'EOF'
You are a friendly, concise AI assistant.
Communicate clearly and directly.
Use emojis sparingly.
Respond in the user's language.
EOF
```

### Кастомное приветствие

В `~/.hermes/config.yaml`:

```yaml
gateway:
  telegram:
    welcome_message: "🤖 Привет! Я твой AI-ассистент. Чем могу помочь?"
```

### Cron-задачи в Telegram

```bash
# Пример: ежедневный дайджест новостей в 9 утра
hermes cron create "0 9 * * *" \
  --name "Daily Digest" \
  --prompt "Создай краткий дайджест последних новостей в AI и технологиях"
```

Результаты придут автоматически в чат с ботом.

---

## ☁️ Cloud & Deployment Options (запуск без ПК)

Hermes можно запустить на облачном сервере — и управлять им через Telegram с телефона.

### 🎯 Быстрый выбор по ситуации

|| Ситуация | Рекомендация | Стоимость |
||---------|-------------|-----------|
|| тестирую | OpenRouter Free + свой ПК | **$0** |
|| 🌍 ограниченная страна | OpenRouter (крипто) + $5 VPS | **$5–$15** |
|| 📚 студент | OpenRouter Free + Fly.io | **$0** |
|| 🔒 приватность | локальные модели + VPS | **$10–$30** |

### VPS Deployment Quickstart

```bash
# 1. Подключись к VPS
ssh root@your-vps-ip

# 2. Установи Hermes
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash

# 3. Настрой Telegram
hermes gateway run

# 4. Запуск при старте системы
crontab -e
# Добавь: @reboot sleep 30 && /root/.hermes/hermes-agent/venv/bin/hermes gateway run
```

### 💳 Крипто-оплата

OpenRouter принимает **USDC/USDT** через MetaMask — работает в любой стране.

---

## 📝 Чек-лист для быстрого старта

- [ ] **Шаг 1:** Создан бот через @BotFather, токен сохранён
- [ ] **Шаг 2:** (Опционально) Описание, аватар, команды настроены
- [ ] **Шаг 3:** Получен Telegram User ID через @userinfobot
- [ ] **Шаг 4:** Токен и ID записаны в `~/.hermes/.env`
- [ ] **Шаг 5:** Установлен `python-telegram-bot`
- [ ] **Шаг 6:** Gateway запущен и работает
- [ ] **Шаг 7:** Бот отвечает в Telegram
- [ ] **Шаг 8:** (По желанию) Бот добавлен в группу
- [ ] **Голосовые:** Установлен `faster-whisper`, настроен русский голос

---

> [!success] 🚀 Готово!
> Теперь у тебя есть полноценный Telegram-бот, подключённый к Hermes Agent. Ты можешь общаться с AI-агентом откуда угодно — с телефона, планшета, другого компьютера. Все возможности (код, исследования, анализ, ответы на вопросы) доступны через Telegram!