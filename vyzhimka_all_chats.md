# Полная выжимка: все чаты (июнь 2026)

Контекст: обучение Make.com и построение бизнеса PolotAI. Ученик — Yan Stukau, 18 лет, Вильнюс. Объясняй просто, по шагам, по-русски. Клиентские материалы — на английском.

---

## Кто я

Имя: Yan Stukau, 18 лет, Вильнюс (Литва).
Бренд: **PolotAI** — агентство бизнес-автоматизации.
Контакты: polotaiauto@gmail.com, Telegram @yanchqqq1.
Позиционирование: AI & Automation Specialist | Make.com.

---

## Что построено и работает

### Make.com — учебные сценарии

**1. Приём заявок (Яндекс Плюс)**
Google-форма → фильтр по магазину → запись в лист «Клиенты Ян» → Telegram-уведомление с wa.me-ссылкой.
Бот «Yandex Plus Alerts», chat_id 918178191.

**2. Продления подписок**
Ежедневно ищет в листе «Клиенты Ян» клиентов с 7 и 1 днём до конца подписки (вспом. колонка «Дней до конца» = окончание − СЕГОДНЯ) → Telegram-карточка с wa.me. Логика фильтра OR.

**3. Живой дашборд**
Таблица «Яндекс Плюс подписки Юра» — карточки + графики на формулах.

**4. ИИ-сортировщик обращений**
Учебный, магазин электроники, англ. Таблица «Support Inbox» → Search Rows (фильтр: Topic пустой, Limit 1) → OpenAI gpt-4o-mini (system-промпт на JSON, Max tokens 200) → Parse JSON → Router: ветка 1 Update Row (всегда), ветка 2 [фильтр sentiment=Negative AND urgency=High] → Slack #urgent-tickets.

**5. E-commerce order pipeline**
HTML-форма заказа с динамическими ценами → Webhook → Set Variables (номер заказа, итого) → Google Sheets → OpenAI (персонализированный email) → Google Docs шаблон → PDF-чек → Gmail с вложением.

**6. Gmail авто-ответ**
Webhook приём заявки с сайта → OpenAI генерирует персональный ответ → Gmail отправляет HTML-письмо.

**7. AI knowledge base (Airtable)**
OpenAI-классификатор (определяет категорию, max 10 tokens) → Airtable Search Records (серверная фильтрация `{Категория} = "Значение"`, 1 операция) → OpenAI генерирует ответ ТОЛЬКО из найденных данных. Без галлюцинаций.
База: «База знаний — Гаджеты», таблица FAQ, три поля: вопрос (Single line text), ответ (Long text), категория (Single select: Доставка, Оплата, Возврат, Гарантия, Товары).

**8. YouTube comment moderator (с Telegram-кнопками)**
Два связанных сценария:
- Сценарий 1: Watch New Comments → OpenAI классифицирует (spam/toxic/positive/question) и пишет черновик ответа → Telegram карточка с inline-кнопками ✅ Да / ❌ Нет.
- Сценарий 2: Webhook ловит callbackQuery → split/get извлекают action и comment_id из callbackQuery.data → trim/last/split извлекают текст ответа → Router: YouTube Reply или Telegram «отклонено».
Custom OAuth через Google Cloud (проект «YouTube Make»), redirect URI: `https://www.make.com/oauth/cb/youtube` и `https://eu1.make.com/oauth/cb/youtube`.

**9. AI support ticket classifier**
(см. п.4 — тот же сценарий, описан выше)

### Make.com — реальные бизнес-сценарии

**10. Dental clinic — напоминания о визите**
Google Sheets «Dental Clinic Demo», лист «Записи». Колонки: Имя, Телефон, Дата визита, Время, Услуга, Статус, Напоминание, Отзыв, Дней до визита.
Search Rows (Дней до визита=1, Статус=Записан, Напоминание не содержит ✅, Limit 1) → Telegram с wa.me → Update Row ✅.

**11. Dental clinic — сбор отзывов**
Search Rows (Статус=Завершён, Отзыв не содержит ✅) → Telegram → Update Row ✅.

**12. Dental clinic — онлайн-запись (Webhook)**
Custom Webhook (`https://hook.eu1.make.com/4353p8673yhguws1ppg74npj4w7m7xvx`) → Google Sheets Add Row → Telegram с switch() для контактных ссылок → Gmail фильтр [contact Equal to "Email"] → Gmail подтверждение.
HTML-форма с радиокнопками (услуги) и 4 способа связи (WhatsApp, Telegram, Телефон, Email).

**13. Cold outreach — рассылка клиникам**
Spreadsheet «PolotAI Outreach Tracker». Колонки: Clinic Name, Email, City, Rating, Status, Date Sent, Date Follow-up, Date Replied, Days since sent.
Формула дней: `=IF(F3="";"";TODAY()-DATEVALUE(LEFT(F3;10)))` (DATEVALUE/LEFT — потому что Make пишет ISO-строки).
Сценарий 1 (Send): Watch New Rows → Gmail (HTML, персонализация: имя клиники + рейтинг) → Update Row Status=Sent.
Известные баги: Watch Rows пере-отправляет при рестарте; крашится на невалидном email (info(eta)citydent.lt).
Сценарий 2 (Follow-up Day 5): спроектирован, не завершён.
Сценарий 3 (Reply tracking): запланирован, не начат.

**ПРОБЛЕМА с рассылкой:** 52 письма отправлено, 0 ответов. Письма попадают в спам у получателей. Причины: новый Gmail + массовая отправка через Make без пауз + продажный subject line + деньги в тексте. Рассылка остановлена на 5–7 дней для восстановления репутации. План: переписать письмо, добавить OpenAI для уникализации, добавить Sleep 90–120 сек между письмами, лимит 5 писем/день.

### Python — реальный проект

**14. Telegram-бот для фестиваля «Good Day Полоцк»**
Python (python-telegram-bot 21.6) + GPT-4o-mini. Фестиваль 27 июня в Полоцке.
7 разделов меню, AI-ассистент по базе знаний фестиваля, фото карты/программы, PDF-призов.
4 уровня браслетов: Quest 🟡, Standart 🔵, VIP 🟢, VIP Deluxe 🟠.
Задеплоен на Railway (worker process) через GitHub (приватный репо `goodday-bot`).
Бартер: бот бесплатно ↔ реклама PolotAI на фестивале.
Токен бота: через переменные окружения (BOT_TOKEN, OPENAI_API_KEY).
Runtime: python-3.13.5 (3.12.7 не работала на Railway).
Railway: ~$3–5/мес за бота, $5 пробных кредитов.

---

## Освоенные навыки (Make.com)

- Триггеры: Watch New Rows, Search Rows, Custom Webhook, Watch New Comments (YouTube)
- Фильтры: AND, OR, равно, не равно, не содержит, пустое
- Модули: Google Sheets (Add/Update/Search Row), Gmail (Send), Telegram (Send Message с inline keyboard, Watch Updates для callbackQuery), OpenAI (Generate a Completion), Parse JSON, Router, Slack (Send Message), Google Docs (Create from Template), YouTube (Reply to Comment)
- Функции: Set Variables, switch(), replace() с regex `/[^0-9]/g`, URL-кодирование, split/get/trim/last для разбора callback data
- Работа с датами: вспомогательные колонки «Дней до...», формула DATEVALUE/LEFT для ISO-дат
- Webhook: приём данных с HTML-формы, Custom Webhook URL
- OAuth: настройка через Google Cloud Console (YouTube Data API v3)
- Экономия операций: Limit=1 при тестах, серверная фильтрация Airtable

## Освоенные навыки (Python)

- python-telegram-bot 21.6 (обработчики, inline keyboards, фото/PDF-отправка)
- OpenAI API (gpt-4o-mini, system prompt с базой знаний)
- Деплой: GitHub → Railway (worker process, environment variables, runtime.txt)

---

## LinkedIn — текущее состояние

**Профиль заполнен:**
- Headline: Make.com & AI Automation Specialist | I build workflows that save businesses hours every week
- About: 460 символов, на английском (уже в профиле)
- Experience: 2 позиции (PolotAI — Founder & Automation Specialist; Good Day Polotsk Festival — Freelance Developer)
- Skills: Make.com, Workflow Automation, OpenAI API, Business Process Automation, Python, Google Sheets, API Integration, Telegram Bot Development, No-Code Development, Airtable, Slack, Google Workspace, JSON, Webhooks

**Company Page PolotAI создана:**
- Логотип: вариант 2 (белый фон, часы + линии скорости + схемные узлы)
- Tagline: We build Make.com + AI automations that save small businesses hours every week.
- About: заполнен (описание услуг, инструменты, контакты)
- Industry: IT Services and IT Consulting
- Size: 0-1
- Type: Предприниматель

**Первый пост опубликован:**
- Тема: YouTube comment moderator
- Формат: проблема → что построил → как работает → CTA
- CTA: "I can automate any business. Tell me what you do, and I'll show you how to save 2+ hours every day."

**Контент-стратегия:**
- 3 типа постов: кейс, инсайт, закулисье
- Язык: английский
- Частота: 2–3 поста/неделю, вт–чт, 8–10 утра
- 9 неиспользованных тем (E-commerce pipeline, AI knowledge base, Subscription reminders, AI ticket classifier, Telegram-бот фестиваля, Онлайн-запись в клинику, «5 процессов для автоматизации», «Почему ваш бот врёт», «Автоматизация vs найм», «Мне 18, я строю агентство»)

**LinkedIn networking:**
- Категории: потенциальные клиенты (owners, managers), автоматизаторы/no-code сообщество, маркетологи/фрилансеры, локальный бизнес Вильнюса
- Где искать: LinkedIn Search + фильтры, комментарии под популярными постами, подписчики конкурентов
- Заметка при добавлении: короткая, без продажи
- Лимит: 15–20 заявок/день

---

## Коммерческое предложение

Документ «Умная клиника» (КП для стоматологий):
- 10 процессов автоматизации
- 3 пакета: Старт (€150 + €29/мес), Бизнес (€350 + €49/мес), Премиум (€600 + €79/мес)
- Ежемесячная оплата обоснована работой ИИ/OpenAI
- Гарантия: доработка бесплатно, если нет результата за 30 дней

---

## Фриланс-платформы

- **LinkedIn** — основной канал (посты + нетворкинг)
- **Fiverr** — бесплатный старт, запланирован
- **Upwork** — зарегистрирован (категория: Web, Mobile & Software Dev; $15/час), но нет бесплатных connects на новом аккаунте — отложен
- **BotPool.ai** — нишевая платформа для AI/ботов
- **Freelancermap.com** — европейский рынок

---

## Ключевые таблицы

| Таблица | Листы / назначение |
|---|---|
| Яндекс Плюс подписки Юра | Клиенты Ян, Клиенты Юра, Отключены, Дашборд |
| Support Inbox | учебный ИИ-сортировщик |
| Dental Clinic Demo | лист «Записи» (напоминания, отзывы, запись) |
| PolotAI Outreach Tracker | cold outreach клиникам |

---

## Инструменты

Make.com, OpenAI (GPT-4o-mini, GPT-4o), Google Sheets, Google Docs, Gmail, Airtable, Telegram Bot API, Slack, YouTube Data API v3, Webhooks, HTML-формы, PDF-генерация, Python (python-telegram-bot 21.6), Railway, GitHub.

---

## Лимит Make.com

Бесплатный план: 1000 операций/мес. Правила: Limit=1 при тестах, серверная фильтрация вместо Make-side, не гонять Run once вслепую.

---

## Принципы обучения

- Понимать каждую строку — не просто копировать, а разбираться
- Не врать про опыт — только реальные проекты
- Стиль общения: прямой, без воды, по-русски
- Клиентские материалы: на английском
- Шаг-за-шагом с названиями кнопок на русском (LinkedIn на русском интерфейсе)

---

## Что дальше

- **Через 5 дней:** возобновить outreach — переписать письмо, добавить OpenAI для уникализации, Sleep между письмами
- **Изучение n8n** — следующий этап
- **LinkedIn:** продолжать посты (9 тем в запасе), наращивать контакты
- **Ресторан дяди (Москва):** реальный потенциальный клиент — собрать автоматизацию (бронирование/отзывы/напоминания), потом добавить в Experience
- **Fiverr:** настроить профиль
