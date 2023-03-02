### Задание №1: Спроектировать БД для сайта интернет-магазина.

Проектируем. Ниже представлены основные таблицы, которые понадобятся для реализации описанного в задании. Большинство constraints в схемах опущены для простоты, но можно было обвесить столбцы чем-нибудь вроде NOT NULL (и прочими CHECK).

Таблица items
---
Используем эту таблицу для хранения всех товаров магазина. Внутри уникальный ID, который является первичным ключом, и необходимая информация о товаре (название, цена...)

Примерная схема (PostgreSQL):

    CREATE TABLE items
	(
		id SERIAL PRIMARY KEY,
		name TEXT,
		price DOUBLE PRECISION
	);
    

Таблица categories
---
Эта таблица нам нужна для хранения категорий, а также ссылки на категорию-родителя. Внутри уникальный первичный ключ, информация о категории (заголовок) и ссылка на родительскую категорию.
Обращаю внимание, что parent_id может быть null. В этом случае у категории не существует родителя (она является корневой).

Примерная схема (PostgreSQL):

    CREATE TABLE categories
	(
		id SERIAL PRIMARY KEY,
		title TEXT,
		parent_id INTEGER REFERENCES categories(id)
	);
    

Таблица items_to_categories
---
Поскольку у нас отношение many to many (у одного товара может быть несколько категорий, и у одной категории может быть несколько товаров), то используем таблицу-посредника для организации связей товар<->категория.

Примерная схема (PostgreSQL):

    CREATE TABLE categories
	(
		id SERIAL PRIMARY KEY,
		item_id INTEGER NOT NULL REFERENCES items(id),
		category_id INTEGER NOT NULL REFERENCES categories(id)
	);
    

Таблица accounts
---
Используем эту таблицу для хранения данных о пользователях. Уникальный ID выступающий первичным ключом, плюс никнейм и зашифрованный пароль.
По желанию можно было бы добавить столбец для назначения роли (или все-таки вынести это в отдельную таблицу, если ролей может быть много).
При регистрации вставляем в таблицу, при аутентификации/авторизации читаем.

Примерная схема (PostgreSQL):

    CREATE TABLE accounts
	(
		id SERIAL PRIMARY KEY,
		username TEXT,
		encrypted_password TEXT
	);
    

Таблица orders
---
Таблица, необходимая для хранения данных о заказах пользователей. Возьмем самые очевидные столбцы: ссылка на пользователя, ссылка на товар, количество купленного и время совершения заказа.

Примерная схема (PostgreSQL):

    CREATE TABLE orders
	(
		id SERIAL PRIMARY KEY,
		account_id INTEGER NOT NULL REFERENCES accounts(id)
		item_id INTEGER NOT NULL REFERENCES items(id),
		amount INTEGER
		ordered_at TIMESTAMP
	);
    
