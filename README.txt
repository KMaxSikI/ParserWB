Тедеграм-бот для парсинга WB

Парсер бот представляет собой простого телеграм-бота, использующего библиотеку aiogram, для поиска товаров на Wildberries. Ниже краткое описание функциональности бота:
1.	Описание бота:
1.1.	Стартовое приветствие:
При старте бота или при вводе команды /start пользователю отправляется приветственное сообщение с его именем, а также предоставляются основные кнопки клавиатуры.
1.2.	Кнопка Описание:
При нажатии кнопки "📄 описание" пользователь получает инструкцию о пользовании ботом.
1.3.	Остановка парсинга:
При нажатии кнопки "❌ стоп" бот останавливает парсинг данных с Wildberries. Пользователю отправляется уведомление о завершении процесса.
1.4.	Запуск парсинга:
При нажатии кнопки "▶️ старт" бот запускает процесс парсинга. Пользователю предлагается ввести категорию товаров для поиска. При этом если пользователь нажимает на кнопку после остановки предыдущего парсинга с помощью "❌ стоп" и вводит запрос, который был остановлен, информация начнет прасится заново.
1.5.	Поиск товаров на Wildberries:
Пользователь вводит категорию товара, и бот отправляет запрос на Wildberries для получения данных.
Полученные данные (артикул, название, рейтинг, отзывы, цена и ссылка) отправляются пользователю в чате.
1.6.	Как использовать бота:
Бот активируется после ввода команды /start.
Используйте кнопки клавиатуры для доступа к функционалу или введите команду для выполнения конкретного действия.
При необходимости остановите парсинг с помощью команды "❌ стоп".
Запустите парсинг снова с помощью команды "▶️ старт" и введите категорию товара.
1.7. Дополнительно Бот записывает всю выгружаемую информацию в базу данных SQLite

==================================================================================================================================

=======================
Веб-сайт на Flask. MVC
=======================

За основу взят парсер данных с Wildberries, как и для ТГ бота.

Что было изменено/добавлено в код:

1. from flask import Flask, render_template, request, redirect, url_for: Импорт необходимых функций и классов из модуля Flask для создания веб-приложения, работы с шаблонами и обработки запросов.

2. app = Flask(__name__) - Создание экземпляра приложения Flask с указанием имени текущего модуля.

3. parsed_data = {} - Инициализация пустого словаря parsed_data, который используется для хранения результатов парсинга.

4. @app.route('/') - Декоратор, который устанавливает маршрут для функции index(). Это обычная главная страница с вводной информацие, чтобы познакомить пользователя с функционалом.

5. @app.route('/contacts/') - Декоратор, который устанавливает маршрут для функции contacts.html. Это тоже стандартная страница где указаны контактыне данные владельцев созданного парсера, при возникновении технических неисправносте, можно написать разрабодчикам и описать проблему. (В данном варианте контакты приведены тестовые, телефоны и почтовые адреса не реальные)

6. @app.route('/form/', methods=['GET', 'POST']): Декоратор, который устанавливает маршрут для функции form(). Он определяет обработчик для HTTP методов GET и POST на странице с формой запроса '/form/'.

7. def form(): Определение функции index(), которая обрабатывает запросы на главную страницу.

8. if request.method == 'POST': Проверка, был ли отправлен POST-запрос.

9. category = request.form['category']: Получение значения из формы с именем 'category'.

10. if category: Проверка, чтобы убедиться, что значение было введено.

11. return redirect(url_for('results', category=category)): Перенаправление на страницу результатов с указанной категорией.

12. return render_template('form.html'): Отображение HTML-шаблона form.html на странице с формой запроса.

13. @app.route('/results/<category>', methods=['GET']): Декоратор, определяющий URL-адрес /results/<category> и метод запроса GET для функции results(). <category> - это переменная часть URL, которая передается в функцию в качестве аргумента.
й
14.def results(category):: Определение функции results() с параметром category, который содержит значение из URL.

15. if category == 'default': Проверка, является ли значение category равным 'default'.

16. return render_template('results.html', category=category, results=None): Если значение category равно 'default', функция вернет шаблон results.html с переменными category равным 'default' и results равным None.

17. if category in parsed_data: Проверка, содержится ли значение category в словаре parsed_data.

18. results = parsed_data[category]: Если данные уже были спарсены для данной категории и хранятся в parsed_data, они присваиваются переменной results.

19. return render_template('results.html', category=category, results=results): Если данные для этой категории уже были сохранены, функция вернет шаблон results.html с переменными category и results, чтобы отобразить сохраненные результаты.

20. url = f'https://search.wb.ru/exactmatch/...': Формирование URL для запроса данных из внешнего источника на основе значения category.

21. resp = requests.get(url): Выполнение GET-запроса к указанному URL для получения данных.

22. resp.encoding = 'utf-8': Установка кодировки для правильного отображения текста.

23. locale.setlocale(locale.LC_ALL, ''): Установка локали для корректного форматирования чисел и дат в соответствии с настройками локали операционной системы.

24. Далее мы проверяем соединение с сервером, если оно успешно равно 200, то начинается формирование данных которые мы парсим согласно запросу. Cамо формирование данных обернуто в try-except это сделано для того, что бы при обработке возможного исключения типа KeyError, которое может возникнуть при доступе к определенному ключу в ответе от сервера Wildberries. Если данные не в том формате или структуре, что ожидалось, программа не остановится с ошибкой, а предоставит альтернативное сообщение пользователю о том, что "Спарсенных данных нет, вернитесь на главную страницу и введите категорию". Так же данное сообщение возникает в том случае если мы пытаемся результат парсинга при первом посещении страниици, при эом не написав какую категорию хотим спарсить, а сразу идем на страницу с результатом.

25. return "Ошибка при запросе данных" - будет срабатывать в том случае если статус ответа от сервера при запросе if resp.status_code == 200 не является успешным, т.е. /= 200.

26. @app.route('/saved_results', methods=['GET']): Декоратор, который определяет URL-адрес /saved_results для функции saved_results(). Этот маршрут обрабатывается при GET-запросах на этот URL.

27. def saved_results(): Определение функции saved_results(), которая вызывается при GET-запросе на URL /saved_results.

28. global parsed_data: Объявление использования глобальной переменной parsed_data, чтобы получить доступ к этой переменной внутри функции. В это переменной сохраняются данные которые сформированы на основании запроса парсинга.

29. return render_template('saved_results.html', parsed_data=parsed_data): Возврат HTML-шаблона saved_results.html с передачей данных из переменной parsed_data в качестве параметра для отображения данных на этой странице. Функция render_template() из библиотеки Flask берет шаблон saved_results.html и передает в него данные из parsed_data для использования при генерации HTML-кода страницы.

30. Для визуализации проделанной работы парсинга, созданы 4 страници:

30.1. Главная страница - несет в себе просто информационный характер, знакомит пользователя со своими возможностями.
30.2. Страница с контактами  - отражена информация о владельцах данного сайта и кому можно написать в случае технических проблем.
30.3. Форма поиска - на данной странице пользователь вводит категорию которую неоходимо спарсить с сайта Wildberries
30.4. Результат - после отправки запроса на парсинг, пользователя автоматически направляют на странице с результатаом парсига.
30.5. Хранилище - страница с сохраненными запросами от пользователя, база данных парсинга.

==================================================================================================================================

=======================================================
Веб-сайт на Flask. MVC. Добавление в базу данных SQlite
=======================================================

Создана база данных с наименованием data.db, в неё загружаются данные по результатам запроса.

Данные формируются в файле data.db. Таблица создана одна т.к. все данные уникальные и не повторяются.

===========================================================
Веб-сайт на Flask. MVC. Добавление в базу данных SQLAlchemy
===========================================================


В данном задании была определена новая ветка проекта "Flask_ParserWB_SQLAlchemy".

Сформирован отделтный файл с модернизированным основным кодом для парсинга товаров с сайта Wildberries

в код добавлены комментарии, сдесь будет короткое описание:
Был установлен SQLAlchemy, добавлен модуль для многопоточности, что бы создать отдельный поток для сохранения данных в базу данных, при этом что бы он не мешал основному потоку и выполнялся быстрее.
Был создан класс для определения полей таблицы базы данных

В results добавлен блок кода который проверяет что если данных в БД нет, то мы записываем их туда, если данные есть, то он проходится по артикулу товаров и если артикулы повторяются, то данные не попадают в БД
данный подход нужен для того чтобы данные не дублировались.

При запуске программы создается отдельная папка "instance" в которой и хранитня база данных. Данная пака добавлена в gitignor, чтобы база данных не попадала в GIT
