# Протокол сети ZeroNet

 - Каждое сообщение зашифровано с помощью [MessagePack](http://msgpack.org/)
 - Каждый запрос имеет 3 параметра:
    * `cmd`: Команда запроса
    * `req_id`: Уникальный идентификатор запроса (простой, инкрементальный, одноразовый на соединение), который клиент должен повторить при ответе на команду.
    * `params`: Параметры запроса
 - Пример запроса: `{"cmd": "getFile", "req_id": 1, "params:" {"site": "1EU...", "inner_path": "content.json", "location": 0}}`
 - Пример ответа: `{"cmd": "response", "to": 1, "body": "content.json content", "location": 1132, "size": 1132}`
 - Пример ответа про ошибку: `{"cmd": "response", "to": 1, "error": "Unknown site"}`


# Начало соединения (хендшейк)
Каждое соединение начинается с хендшейка посылкой запроса на целевой адрес сети:

Параметр             | Описание
                 --- | ---
**crypt**            | Null/None, используется только в ответе
**crypt_supported**  | Массив методов шифрования соединения, поддерживаемых клиентом
**fileserver_port**  | Клиентский порт файлового сервера
**onion**            | (Используется только в tor) Клиентский onion адрес
**protocol**         | Версия протокола, используемая клиентом (v1 или v2)
**port_opened**      | Статус открытости клиентского порта клиента
**peer_id**          | (Не используется в tor) Клиентский идентификатор пира
**rev**              | Номер ревизии клиента
**version**          | Версия клиента
**target_ip**        | IP адрес сервера

Цель инициализирует шифрование на сокете, основываясь на `crypt_supported`, и затем отвечает:

Возвращаемый ключ    | Описание
                 --- | ---
**crypt**            | Используемое шифрование
**crypt_supported**  | Массив методов шифрования соединения, поддерживаемый сервером
**fileserver_port**  | Серверный порт файлового сервера
**onion**            | (Используется только в tor) Серверный onion адрес
**protocol**         | Версия протокола, используемая сервером (v1 или v2)
**port_opened**      | Статус открытости серверного порта клиента
**peer_id**          | (Не используется в tor) Серверный идентификатор пира
**rev**              | Номер ревизии сервера
**version**          | Версия сервера
**target_ip**        | IP адрес сервера

> **Кстати:** Шифрование не используется в соединениях .onion, поскольку сеть Tor предоставляет транспортную безопасность по умолчанию.
> **Кстати:** Вы также можете неявно инициализировать SSL перед хендшейком, если считаете, что он поддерживается удаленным клиентом.

**Пример**:

Посылаем хендшейк:

```json
{
  "cmd": "handshake",
  "req_id": 0,
  "params": {
    "crypt": None,
    "crypt_supported": ["tls-rsa"],
    "fileserver_port": 15441,
    "onion": "zp2ynpztyxj2kw7x",
    "protocol": "v2",
    "port_opened": True,
    "peer_id": "-ZN0056-DMK3XX30mOrw",
    "rev": 2122,
    "target_ip": "192.168.1.13",
    "version": "0.5.6"
  }
}
```

Ответ:

```
{
 "protocol": "v2",
 "onion": "boot3rdez4rzn36x",
 "to": 0,
 "crypt": None,
 "cmd": "response",
 "rev": 2092,
 "crypt_supported": [],
 "target_ip": "zp2ynpztyxj2kw7x.onion",
 "version": "0.5.5",
 "fileserver_port": 15441,
 "port_opened": False,
 "peer_id": ""
}
```

# Запросы пира

#### getFile _site_, _inner_path_, _location_, _[file_size]_
Запрос файла с клиента

Параметр             | Описание
                 --- | ---
**site**             | Адрес сайта (например: 1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**inner_path**       | Путь к файлу относительно каталога сайта
**location**         | Запрос файла с этого байта (max 512 байт посылается при запросе, поэтому Вам надо сделать множество запросов для получения большого файла)
**file_size**        | Общий размер запрашиваемого файла (опционально)

**Ответ**:

Возвращаемый ключ    | Описание
                 --- | ---
**body**             | Содержимое запрошенного файла
**location**         | Расположение последнего переданного байта
**size**             | Общий размер файла


---

#### streamFile _site_, _inner_path_, _location_, _[file_size]_
Поток файла с клиента

**Ответ**:

Возвращаемый ключ   | Описание
                --- | ---
**stream_bytes**    | Длина файла после MessagePack

Чтобы избежать сериализации больших бинарных строк python-msgpack, тело файла присоединяется непосредственно после MessagePack. Например,

```
> {"cmd": "streamFile", "id": 1, "inner_path": "content.json", "size": 1234}
< {"cmd": "response", "to": 1, "stream_bytes": 1234}
< содержимое файла
```

> Детали реализации в ZeroNet: Для сегментов файла, больших 256 кБ, стриминг выполняется по умолчанию.

---


#### ping
Проверка, что клмент ещё жив

**Ответ**:

Возвращаемый ключ    | Описание
                 --- | ---
**body**             | Pong


---


#### pex _site_, _peers_, _need_
Обмен пирами с клиентом.
Данные пира упаковываются в 6 байт (4 байта для IP с помощью inet_ntoa + 2 байта для порта)

Параметр             | Описание
                 --- | ---
**site**             | Адрес сайта (например: 1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**peers**            | Список пиров известных запрашивающему (упакованный)
**peers_onion**      | Список пиров Tor Onion известных запрашивающему (упакованный)
**need**             | Количество пиров, сколько хочется получить

**Ответ**:

Возвращаемые ключи  | Description
                --- | ---
**peers**           | Список IPv4 пиров для этого сайта (упакованный)
**peers_onion**     | Список Tor Onion пиров для этого сайта (упакованный)

Каждый элемент в списке `peers` - это упакованный IPv4 адрес.

IP адрес  | Порт
--------- | ----
`4 байта` | `2 байта`

Каждый элемент в списке `peers_onion`  - это упакованный адрес сети Tor Onion.

Onion адрес закодированный B32 | Порт
------------------------- | ----
`binary_str[0:-2]`        | `binary_str[-2:]`

Для восстановления адреса onion пропустите первую часть через `base64.b32encode` и добавьте `.onion` к полученному значению.

---

#### update _site_, _inner_path_, _body_, _[diffs]_
Обновление файла сайта.


Параметр             | Описание
                 --- | ---
**site**             | Адрес сайта (например: 1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**inner_path**       | Путь к файлу относительно каталога сайта
**body**             | Полное содержимое обновлённого content.json
**diffs** (опционально) | [Опкоды различий](#possible-diff-opcodes) для изменённых файлов в content.json

**Ответ**:

Возвращаемый ключ    | Описание
                 --- | ---
**ok**               | Благодарность при успешном обновлении :)

##### Формат различий

Словарь, содержащий изменения

 - Ключ: путь к изменённому файлу относительно content.json (напр.: `data.json`)
 - Значение: Список опкодов различий для файла (напр.: `[['=', 5], ['+', '\nhello new line'], ['-', 6]]`)

##### Возможные опкоды различий:

Опкод                                    | Описание
                                     --- | ---
**['=', число таких же символов]**       | Часть файла без изменений (напр.: `['=', 5]`)
**['+', новый текст]**                   | Добавленые символы (напр.: `['+', '\nhello new line']`)
**['-', число удалённых символов]**      | Полное содержание обновлённого файла (напр.: `['-', 6]`)

Когда обновления приняты, клиент пытается восстановить файл использую список изменений.
Если есть различия при сравнении хэша sha с хэшем в content.json (версии файлов различны), он автоматически загружает файл полностью с источника обновлений.

> __Кстати:__ Такие патчи ограничены до 30 кБ на файл и применяются только для файлов типа .json

---

#### listModified _site_, _since_
Список файлов content.json, изменённых после указанного времени. Используется для получения контанта, изменённого пользователями сайта.


Параметр             | Описание
                 --- | ---
**site**             | Адрес сайта (например: 1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**since**            | Время, после которого изменялись content.json

**Ответ**:

Возвращаемый ключ    | Описание
                 --- | ---
**modified_files**   | Ключ: content.json inner_path<br>Значение: Время последнего изменения

**Пример**:

```json
> zeronet.py --silent peerCmd 127.0.0.1 15441 listModified "{'site': '1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8', 'since': 1497507030}"
{
  "to": 1,
  "cmd": "response",
  "modified_files": {
    "data/users/1NM9k7VJfrb1UWw5agAvyRfSn3ws1wTJ5U/content.json": 1497579272,
    "data/users/1QEfmMwKVxgR4rkREbdJYjgUmF3Zy8pwHt/content.json": 1497565986,
    "data/users/16NS3rBdW9zpLmLSQoD8nLTtNVsRFtVBhd/content.json": 1497575039,
    "data/users/1CjXarXgvcNeCJ2nMQxUi4DRFWp3GEur2W/content.json": 1497513808,
    "data/users/1L5rGDgTs4W2V7gekSvJNhKa7XaHkVwotD/content.json": 1497615798,
    "data/users/1LWuc6JBhUGrKEAh1aPrPU85dEMcKmg3pS/content.json": 1497594716,
    "data/users/1KdnTJVBGzEZrJppFZtzfG9chukuMv8xSb/content.json": 1497584640,
    "data/users/1GMNmr2bDPbT4c8yVnyCoDHke52CNCdqAa/content.json": 1497614188,
    "data/users/1GRm9rED83Tkfi3iWS9m3LWHiRpPZehWLd/content.json": 1497827772,
    "data/users/12Ugp53jiMdvj1Kxa1w7c2LcXUBdGPs1oK/content.json": 1497692901,
    "data/users/1F6BMqittjWUStzUbRXm2kG2GQ3RdBLqFQ/content.json": 1497571485,
    "data/users/1GgNo3CmxPd7n2pMSF3uyqf1XHvgtTUqCe/content.json": 1497560829,
    "data/users/16nArdxrSaNThNp83kL8E6NLL9WD98iUne/content.json": 1497627929,
    "data/users/16CAJkbfNRxNJq4aKdrZ2MSYFfFGvQ8JPi/content.json": 1497664899,
    "data/users/1DrBS2sTD3BX5BBxG8eqYsxXSvGt9kc5HE/content.json": 1497632000,
    "data/users/19sggoAZ4hcorrrfWoFWP9rwfpVsL29cnZ/content.json": 1497928134,
    "data/users/1NYpJupegoTXL4cFpkNdLNJ4XaAhTNhPe1/content.json": 1497535771,
    "data/users/1R67TfYzNkCnh89EFfGmXn5LMb4hXaMRQ/content.json": 1497691787,
    "data/users/1C9HXUYFSVafLxanwkaFPZRcRgCEGsj2Cn/content.json": 1497572833,
    "data/users/1LgoHzNGWeijeZbJ8a1YgGjMCnjaM4BWG/content.json": 1497620232,
    "content.json": 1497623639
  }
}
```

---


#### getHashfield _site_
Получить скачанные клиентом [id опциональных файлов](#optional-file-id).

Параметр             | Описание
                 --- | ---
**site**             | Адрес сайта (например: 1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)

**Ответ**:

Возвращаемый ключ    | Описание
                 --- | ---
**hashfield_raw**    | ID опциональных файлов, закодированные с помощью `array.array("H", [1000, 1001..]).tostring()`

**Пример**:
```json
> zeronet.py --silent peerCmd 192.168.1.13 15441 getHashfield "{'site': '1Gif7PqWTzVWDQ42Mo7np3zXmGAo3DXc7h'}
{
  'to': 1,
  'hashfield_raw': 'iG\xde\x02\xc6o\r;...',
  'cmd': 'response'
}
```

---


#### setHashfield _site_, _hashfield_raw_
Устанавливает список [id опциональных файлов](#optional-file-id), которые имеет запрашивающий клиент.

Параметр             | Описание
                 --- | ---
**site**             | Адрес сайта (например: 1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**hashfield_raw**    | ID опциональных файлов, закодированные с помощью `array.array("H", [1000, 1001..]).tostring()`

**Ответ**:

Возвращаемый ключ    | Описание
                 --- | ---
**ok**               | Обновлён


---


#### findHashIds _site_, _hash_ids_
Запрашивает, знает ли клиент пира с необходимыми id опциональных файлов
Queries if the client know any peer that has the requested hash_ids

Параметр             | Описание
                 --- | ---
**site**             | Адрес сайта (например: 1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**hash_ids**         | Список id разыскиваемых опциональных файлов

**Ответ**:

Возвращаемый ключ    | Описание
                 --- | ---
**peers**            | Ключ: Id опционального файла<br>Значение: Список ipv4 пиров, закодированных `socket.inet_aton(ip) + struct.pack("H", port)`
**peers_onion**      | Ключ: Id опционального файла<br>Значение: Список onion пиров, закодированных `base64.b32decode(onion.replace(".onion", "").upper()) + struct.pack("H", port)`

**Пример**:
```json
> zeronet.py --silent peerCmd 192.168.1.13 15441 findHashIds "{'site': '1Gif7PqWTzVWDQ42Mo7np3zXmGAo3DXc7h', 'hash_ids': [59948, 29811]}"
{
  'to': 1,
  'peers': {
    29811: [
      'S&9\xd3Q<',
      '>f\x94\x98N\xa4',
      'gIB\x90Q<',
      '\xb4\xady\xf7Q<'
    ],
    59948: [
      'x\xcc>\xf6Q<',
      'S\xa1\xddkQ<',
      '\x05\xac\xe8\x8dQ<',
      '\x05\xc4\xe1\x93Q<',
      'Q\x02\xed\nQ<'
    ]
  },
  'cmd': 'response',
  'peers_onion': {
    29811: ['\xc7;A\xce\xbc\xd9O\xe2w<Q<'],
    59948: ['\xc7;A\xce\xbc\xd9O\xe2w<Q<']
  }
}
```

##### Id опционального файла
Числовое представление первых 5 символов хэша:
```
>>> int("ea2c2acb30bd5e1249021976536574dd3f0fd83340e023bb4e78d0d818adf30a"[0:4], 16)
59948
```

---

#### checkport _port_
Проверяет запрашиваемый порт другого пира.


Параметр             | Описание
                 --- | ---
**port**             | Проверяемый порт.

**Ответ**:

Возвращаемый ключ    | Описание
                 --- | ---
**status**           | Статус порта ("open" или "closed")
**ip_external**      | Внешний IP запрашивающего

---

# Плагин Bigfile

#### getPieceFields _site_

Возвращает все [piecefield](#bigfile-piecefield) большого файла, которые клиент имеет в словаре для этого сайта.

Параметр             | Описание
                 --- | ---
**site**             | Запрашиваемый сайт


**Ответ**:

Возвращаемый ключ      | Описание
                   --- | ---
**piecefields_packed** | Ключ: sha512/256 большого файла [merkle root hash](#bigfile-merkle-root)<br>Значение: Упакованный [piecefield](#bigfile-piecefield)

---

#### setPieceFields _site_, _piecefields_packed_

Устанавливает клиентский [piecefields](#picefield) для данного сайта.

Параметр               | Описание
                   --- | ---
**site**               | Запрашиваемый сайт
**piecefields_packed** | Ключ: sha512/256 большого файла [merkle root hash](#bigfile-merkle-root)<br>Значение: Упакованный [piecefield](#bigfile-piecefield)


**Ответ**:

Возвращаемый ключ    | Описание
                 --- | ---
**ok**               | Выполнено


##### Bigfile piecefield

Содержит информацию о скачанных кусках большого файла в простой строке значений 0 и 1. (1 = скачан, 0 = не скачан)

> __Пример__: `1110000001` означает файл размером 9..10 МБ и клиент скачал первые 3 МБ и последний 1 МБ кусками по 1 МБ.

**Формат упаковки**:

Преобразует строку в список целых значений счётчиков повторяющихся символов начиная с `1`.

> __Пример__: `1110000001` в `[3, 6, 1]`, `0000000001` в `[0, 9, 1]`, `1111111111` в `[10]`

После преобразования возвращается более эффективный [типизированный массив](https://docs.python.org/2/library/array.html) с помощью `array.array('H', piecefield)`

##### Bigfile merkle root

Во время процедуры хэширования большого файла в дополнению к sha512/256 хэшам кусков в файле [piecemap](#bigfile-piecemap), алгоритм также вычисляет SHA-512/256 merkle root файла с помощью [merkle-tools](https://github.com/tierion/merkle-tools).
merkle root используется только для идентификации большого файла, не для верификации кусков (пока).

> __Кстати__: merkle root выбран для идентификации файла, а не актуальный его SHA-512/256 хэш. Очевидно, что использование последнего приводит к повторному хэшированию файла (один раз для piecemap и один раз для файла целиком).

> __Кстати__: merkle root не используется для верификации целостности кусков или всего большого файла, поскольку может привести к увеличению используемой полосы пропускания и объёма при передаче и хранении merkle-proofs при частичной верификации, чем сам файл хэш-карты.

##### Bigfile piecemap

Содержит SHA-512/256 хэши кусков файла. Размер кусков и имя файла piecemap определяется в `content.json`, напр.:

```
...
 "files_optional": {
  "bigfile.mp4": {
   "piece_size": 1048576,
   "piecemap": "bigfile.mp4.piecemap.msgpack",
   "sha512": "d1f0d150e1e73bb1e684d370224315d7ba21e656189eb646ef7cc394d033bc2b",
   "size": 42958831
  },
...
```

Имеет следующую структуру данных, файл piecemap упакован в формат [msgpack](https://msgpack.org/):

```
{
  b'bigfile.mp4': {b'sha512_pieces': [
    b"e\xde\x0fx\xec\xc5LZ9\x0e\xe7\x85E\x1b\xd5\xe4C'\xe7req\xe3<\xff\\\xbb\xc8b\xc2\xc1\x8e",
    b'\xef\xe8\xed\xfe\x16/\x96\xdb;;\x06n[8_\x06\x9ak|\xe1\x9f\xe1\xaf\x87\x96\xdd\xfd\x9bEf\xd9!',
    b'\x1c\xd6-\x1f\xce\xde{\xcd\x01\x93un =D\x0brmB-\xd1\x8c\xbf\xfe\xca\x8a\x1c\xf60\xbb\xedD',
    b'\x1aQdF\xd2\xbc\xdff{\xb7\x89\xf2\xd3\r\xa9\xe1\xefA-V\x18\xa4\xc8e\x13\x88v\x13\\&\xfbW',
    ...
  ]}
}
```
