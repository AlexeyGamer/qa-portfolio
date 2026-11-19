# qa-portfolio

# Баг: reCAPTCHA не инициализируется (форма не проходит проверку)

**Приоритет:** Высокий (функционал отправки формы нарушен)  
**Статус:** Новый  
**Компонент:** Плагин Contact Form 7 / reCAPTCHA v3

## Описание
При попытке отправки формы (или при загрузке страницы) виджет reCAPTCHA не инициализируется. В результате форма либо не отправляется, либо отправляется без проверки на бота.

## Шаги воспроизведения
1. Открыть страницу с формой на `blog.skillfactory.ru`.
2. Открыть DevTools (F12) → вкладка Console.
3. Обновить страницу (Ctrl+R).
4. В консоли увидеть ошибку `Uncaught ReferenceError: grecaptcha is not defined`.
5. Перейти во вкладку Network, отфильтровать по `recaptcha`.
6. Убедиться, что запрос к `recaptcha_ru.js` имеет статус 200 OK.
7. Перейти во вкладку Sources, открыть файл плагина CF7 и найти вызов `grecaptcha.execute`.

## Ожидаемый результат
Скрипт reCAPTCHA корректно инициализируется. Переменная `grecaptcha` доступна в глобальной области видимости. При отправке формы генерируется токен, который отправляется на сервер.


## Фактический результат

1. В консоли ошибка: `Uncaught ReferenceError: grecaptcha is not defined`.
2. Внутренняя ошибка скрипта: `Invalid site key or not loaded in api.js`.
3. Скрипт `recaptcha_ru.js` загружается успешно (статус 200), но не создаёт объект `grecaptcha`.
4. Форма не проходит проверку reCAPTCHA.

## Доказательства (артефакты)
<img width="1103" height="1207" alt="01_console_grecaptcha_error.png" src="https://github.com/user-attachments/assets/87c65a28-6cfd-41d7-a264-b501d6ef00a9" />
<img width="972" height="961" alt="02_console_apijs_error.png" src="https://github.com/user-attachments/assets/33f8bce6-9837-4f33-ba66-688020c55d37" />
<img width="896" height="1237" alt="03_sources_cf7_code.png" src="https://github.com/user-attachments/assets/0fd0d3e8-8a97-4fe5-948f-4ab6901ad504" />
<img width="972" height="965" alt="04_network_recaptcha_200.png" src="https://github.com/user-attachments/assets/4539632c-dc8a-4614-866e-045ad5cd5427" />



## Анализ и вероятная причина
Проблема не в блокировке скриптов или отсутствии интернета. Скрипт загружается (Network 200), но падает на этапе валидации ключа сайта (Site Key). Скорее всего, в настройках плагина Contact Form 7 указан неверный Site Key или он не соответствует домену `blog.skillfactory.ru`.

## Окружение
- Браузер: Яндекс Браузер 25.10.1 (Chromium 130)
- ОС: Windows 11 Pro 64-bit
- URL: blog.skillfactory.ru
- Дата: 19.11.2026
