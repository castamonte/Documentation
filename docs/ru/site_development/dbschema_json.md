# Структура dbschema.json

[Пример файла dbschema.json](https://github.com/HelloZeroNet/ZeroTalk/blob/master/dbschema.json)

Этот код выполняет следующее:

 - Если принят обновлённый файл data/users/*/data.json (например, пользователь написал какое-то сообщение):
   - Каждая строка в `data["topics"]` загружается в таблицу `topic`
   - Каждый ключ в `data["comment_votes"]` загружается в строку таблицы `comment_vote` в столбец `comment_hash` и значение - в столбец `vote` той же строки
 - Если принят обновлённый файл data/users/content.json (например, создан новый пользователь):
   - Ключи `"user_id", "user_name", "max_size", "added"` со значениями `content["include"]` загружаются в таблицу `user` и ключ запоминается как `path`

> Кстати: [Некоторые ограничения](content_json/#regular-expressions-limitations) накладываются на регулярные выражения во избежание возможной уязвимости ReDoS.

```json

{
  "db_name": "ZeroTalk", # Имя базы данных (используется только для отладки)
  "db_file": "data/users/zerotalk.db", # Файл базы данных относительно каталога сайта
  "version": 2, # 1 = Таблица json имеет столбец с каталогом и именем файла
                # 2 = Таблица json имеет отдельные столбцы для каталога и имени файла
                # 3 = Как и версия 2, но также имеет столбец для сайта (для связанных сайтов)
  "maps": { # Маппинг json в базу данных
    ".*/data.json": { # Паттерн регулярного выражения файла относительно файла базы данных
      "to_table": [ # Загрузка значений в таблицу
        {
          "node": "topics", # Чтение значения ключа из data.json[topics]
          "table": "topic" # Заполнение данных в таблицу topic
        },
        {
          "node": "comment_votes", # Чтение значения ключа из data.json[comment_votes]
          "table": "comment_vote", # Заполнение данных в таблицу comment_vote
          "key_col": "comment_hash",
            # data.json[comment_votes] это простой словарь, ключи словаря
            # загружаются в таблицу comment_vote в столбец comment_hash

          "val_col": "vote"
            # значения словаря data.json[comment_votes] загружаются в таблицу comment_vote в столбец vote

        }
      ],
      "to_keyvalue": ["next_message_id", "next_topic_id"]
        # Загрузка data.json[next_topic_id] в таблицу ключ-значение
        # (ключ: next_message_id, значение: data.json[next_message_id])

    },
    "content.json": {
      "to_table": [
        {
          "node": "includes",
          "table": "user",
          "key_col": "path",
          "import_cols": ["user_id", "user_name", "max_size", "added"],
            # Импорт только этих столбцов в таблицу user
          "replaces": {
            "path": {"content.json": "data.json"}
              # Замена content.json на data.json
              # в столбце path (требуется для соединения)
          }
        }
      ],
      "to_json_table": [ "cert_auth_type", "cert_user_id" ]  # Сохраняем cert_auth_type и cert_user_id прямо в таблице json (упрощая и ускоряя запросы данных)
    }
  },
  "tables": { # Определения таблиц
    "topic": { # Определение таблицы topic
      "cols": [ # Столбцы таблицы
        ["topic_id", "INTEGER"],
        ["title", "TEXT"],
        ["body", "TEXT"],
        ["type", "TEXT"],
        ["parent_topic_hash", "TEXT"],
        ["added", "DATETIME"],
        ["json_id", "INTEGER REFERENCES json (json_id)"]
      ],
      "indexes": ["CREATE UNIQUE INDEX topic_key ON topic(topic_id, json_id)"],
        # Индексы создаются автоматически

      "schema_changed": 1426195822
        # Время последнего изменения схемы, если клиентская версия отличается, то
        # старая автоматически удаляется, создаётся новая таблица и данные переносятся в неё

    },
    "comment_vote": {
      "cols": [
        ["comment_hash", "TEXT"],
        ["vote", "INTEGER"],
        ["json_id", "INTEGER REFERENCES json (json_id)"]
      ],
      "indexes": ["CREATE UNIQUE INDEX comment_vote_key ON comment_vote(comment_hash, json_id)", "CREATE INDEX comment_vote_hash ON comment_vote(comment_hash)"],
      "schema_changed": 1426195826
    },
    "user": {
      "cols": [
        ["user_id", "INTEGER"],
        ["user_name", "TEXT"],
        ["max_size", "INTEGER"],
        ["path", "TEXT"],
        ["added", "INTEGER"],
        ["json_id", "INTEGER REFERENCES json (json_id)"]
      ],
      "indexes": ["CREATE UNIQUE INDEX user_id ON user(user_id)", "CREATE UNIQUE INDEX user_path ON user(path)"],
      "schema_changed": 1426195825
    },
    "json": {  # Формат таблицы json требуется только если Вы указали где-либо паттерн to_json_table
        "cols": [
            ["json_id", "INTEGER PRIMARY KEY AUTOINCREMENT"],
            ["directory", "TEXT"],
            ["file_name", "TEXT"],
            ["cert_auth_type", "TEXT"],
            ["cert_user_id", "TEXT"]
        ],
        "indexes": ["CREATE UNIQUE INDEX path ON json(directory, site, file_name)"],
        "schema_changed": 4
    }
  }
}
```

## Пример файла data.json
```json
{
  "next_topic_id": 2,
  "topics": [
    {
      "topic_id": 1,
      "title": "Newtopic",
      "body": "Topic!",
      "added": 1426628540,
      "parent_topic_hash": "5@2"
    }
  ],
  "next_message_id": 19,
  "comments": {
    "1@2": [
      {
        "comment_id": 1,
        "body": "New user test!",
        "added": 1423442049
      }
    ],
    "1@13": [
      {
        "comment_id": 2,
        "body": "hello",
        "added": 1424653288
      },
      {
        "comment_id": 13,
        "body": "test 123",
        "added": 1426463715
      }
    ]
  },
  "topic_votes": {
    "1@2": 1,
    "4@2": 1,
    "2@2": 1,
    "1@5": 1,
    "1@6": 1,
    "3@2": 1,
    "1@13": 1,
    "4@5": 1
  },
  "comment_votes": {
    "5@5": 1,
    "2@12": 1,
    "1@12": 1,
    "33@2": 1,
    "45@2": 1,
    "12@5": 1,
    "34@2": 1,
    "46@2": 1
  }
}
```

## Пример файла content.json

```json
{
  "files": {},
  "ignore": ".*/.*",
  "includes": {
    "13v1FwKcq7dx2UPruFcRcqd8s7VBjvoWJW/content.json": {
      "added": 1426683897,
      "files_allowed": "data.json",
      "includes_allowed": false,
      "max_size": 10000,
      "signers": [
        "13v1FwKcq7dx2UPruFcRcqd8s7VBjvoWJW"
      ],
      "signers_required": 1,
      "user_id": 15,
      "user_name": "meginthelloka"
    },
    "15WGMVViswrF13sAKb7je6oX3UhXavBxxQ/content.json": {
      "added": 1426687209,
      "files_allowed": "data.json",
      "includes_allowed": false,
      "max_size": 10000,
      "signers": [
        "15WGMVViswrF13sAKb7je6oX3UhXavBxxQ"
      ],
      "signers_required": 1,
      "user_id": 18,
      "user_name": "habla"
    }
  }
}
```
