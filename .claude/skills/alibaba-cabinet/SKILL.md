---
name: alibaba-cabinet
description: Send supplier inquiries from the operator's own logged-in Alibaba account through their connected browser (Claude in Chrome / desktop app / remote device). Use when the user asks to message factories on Alibaba, send inquiries from the Alibaba cabinet, or work inside their Alibaba account ("напиши фабрике в алибабе", "отправь запрос из кабинета").
---

# Alibaba Cabinet — запросы фабрикам из кабинета оператора

## Жёсткие правила (нарушение = стоп)

1. **Только через браузер оператора.** Работает ТОЛЬКО когда в сессии доступны браузерные
   инструменты пользователя: `mcp__claude-in-chrome__*`, `mcp__Claude_Browser__*` или
   `mcp__remote-devices__*` (или их enable-тулза). Перед первым шагом прочитать
   соответствующий скилл (anthropic-skills:chrome-browser / built-in-browser / computer-use).
   Если инструментов нет — НЕ изобретать обход: сказать оператору поставить Claude in Chrome
   или Claude Desktop, дать ссылку на claude.ai/chrome, и предложить альтернативу (email/формы).
2. **Никогда не спрашивать и не вводить пароль/2FA от Alibaba.** Сессия должна быть уже
   залогинена оператором. Логин-форма на экране → остановиться, попросить оператора войти
   самому.
3. **Никаких покупок, оплат, Trade Assurance-заказов, изменений настроек аккаунта.** Только:
   поиск, открытие витрин/карточек, чтение сообщений, заполнение и отправка inquiry /
   Contact Supplier, ответы в Message Center. Любая кнопка с деньгами — стоп и вопрос.
4. **Каждое НОВОЕ исходящее сообщение** (новой фабрике) — по одобренному шаблону; получатели
   из recipients.json (approved) или явное указание оператора в чате. Ответы в существующих
   тредах — продолжение одобренной переписки, слать можно.
5. Темп человеческий: одна фабрика за раз, без массовой рассылки за минуты (антибот).

## Шаблон inquiry (EN, подставить стиль/акцент под фабрику)

Использовать текущий RFQ-текст из petshop/outbox/ (сжатая версия для формы, лимит часто
~4000 знаков): кто мы (518 Group LLC, small dogs ≤5.5 kg, custom from tech packs, NOT
catalog+logo), 6 стилей + амуниция, 7 вопросов (техпаки/лекальщик, окраска+лаб-дипы,
стоимость разработки, MOQ и FOB 300/500/1000, тримы, сертификаты, сроки/порт/оплата).
Контакт для ответа: адрес, который читает агент (Gmail-ящик оператора), плюс кабинет.

## Порядок работы

1. Свериться с petshop/recipients.json и quotes-tracker.md — кому и что уже отправлено;
   дубль не слать.
2. Открыть витрину фабрики (URL из suppliers.md), проверить, что это та компания.
3. Contact Supplier → заполнить тему и текст, приложить файл если форма позволяет
   (design brief; полные техпаки — только финалистам по решению оператора).
4. Скриншот заполненной формы ДО отправки (в лог), отправить, скриншот подтверждения.
5. Записать в quotes-tracker.md и recipients.json (канал: alibaba-cabinet, дата), коммит+push.
6. Проверять Message Center на ответы при каждом заходе; новые ответы — в трекер и в чат
   по-русски.

## Приоритетные цели (на момент создания скилла)

Weichong (weichongchongwu.en.alibaba.com — 4-лапые, сайты за капчой, других каналов нет),
затем Tier-2 из suppliers.md без email: Tlon, Petisland, Runhong, SingYee, Huangbo,
Sinotex Yijia, Chanch. Также Message Center: входящие от фабрик, писавших в кабинет.
